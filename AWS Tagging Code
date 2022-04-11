#!/usr/local/bin/python3
import boto3
from pprint import pprint

### BOTO OBJECTS #############################################################
client = boto3.client('cloudformation')
ec2 = boto3.resource('ec2')
### END BOTO OBJECTS #########################################################



### FUNCTIONS ################################################################
def get_asg_instances(asg_name):
  asg_client = boto3.client('autoscaling')
  ec2_instances = []

  asg = asg_client.describe_auto_scaling_groups(AutoScalingGroupNames=[asg_name])
  pprint(asg['AutoScalingGroups'][0]['Instances'])

  for instance in asg['AutoScalingGroups'][0]['Instances']:
    ec2_instance = ec2.Instance(instance['InstanceId'])
    ec2_instances.append(ec2_instance)

  return ec2_instances
### END FUNCTIONS ############################################################


next_token = None
ets_alpha_analytics_stacks = []

stacks = client.describe_stacks()
while True:
    for stack in stacks['Stacks']:
        if 'CREATE_COMPLETE' not in stack['StackStatus']:
            continue

        for tag in stack['Tags']:
            if tag['Key'].lower() == 'microservice':
                if tag['Value'] in ['BusinessObjects','Tableau','Vertica']:
                    ets_alpha_analytics_stacks.append(stack)

    if not 'NextToken' in stacks:
        break

    stacks = client.describe_stacks(NextToken=stacks['NextToken'])

pprint(ets_alpha_analytics_stacks)

resources = {}
for ets_stack in ets_alpha_analytics_stacks:
  stack_name = ets_stack["StackName"]
  print(f"\n{stack_name}")
  resources[stack_name] = client.describe_stack_resources(StackName=stack_name)

pprint(resources)

ec2_instances = []
for stack_name, stack_resources in resources.items():
  for resource in stack_resources['StackResources']:
    if 'AWS::EC2::Instance' == resource['ResourceType']:
      instance = ec2.Instance(resource['PhysicalResourceId'])
      ec2_instances.append(instance)
      if 'AWS::AutoScaling::AutoScalingGroup' == resource['ResourceType']:
        print(f"Do ASG Tag Update on: { resource['PhysicalResourceId'] }")
        ec2_instances += get_asg_instances(resource['PhysicalResourceId'])

for ec2_instance in ec2_instances:
  print(f"ec2 instance: {ec2_instance.id}")
  response = ec2.create_tags(
    DryRun=True,
    Resources=[
      str(ec2_instance.id),
    ],
    Tags=[
      {
        'Key': 'OperationsTeam',
        'Value': 'EmergingTechnologyServices-Alpha-Navy'
      },
    ]
  )

print(response)
