# Encrypt EBS Volumes

- [Overview](#overview)
- [Note](#note)
- [Detailed Steps](#detailed-steps)
- [Risks](#risks)
- [Criteria for Success](#criteria-for-success)
- [Back-Out Plan](#back-out-plan)

## Overview

* Encrypt EBS Volumes

## Note

- We would need to start and stop the instance when its of low in usage.

- Make a note of Volume Id, Device name, Volume Size, Availability Zone.
- This step is a workaround when we don't have Remove scale in protection capability in ASG. Make a note of Advanced configurations of ASGs and adjust them to following values.
    - Instance scale-in protection
        - Not protected from scale in

    - Suspended processes
        - InstanceRefresh, HealthCheck, Launch, AlarmNotification, RemoveFromLoadBalancerLowPriority, Terminate, AZRebalance, AddToLoadBalancer, ScheduledActions, ReplaceUnhealthy

- Check for the availability of host and disk space on the instance.


## Detailed Steps

1. Select your unencrypted volume
2. Select ‘Actions’ – ‘Create Snapshot’
3. When the snapshot is complete, select ‘Snapshots’ under ‘Elastic Block Store’ Select your newly created snapshot
4. Select ‘Actions’ – ‘Copy’
5. Check the box for ‘Encryption’
6. Select the CMK for KMS to use as required
7. Click ‘Copy’
8. Select the newly created snapshot
9. Select ‘Actions’ – ‘Create Volume’
10. You will notice that the normal ‘Encryption’ option is set to ‘True.’ Because the snapshot is itself encrypted, this cannot be modified. The volume now created from this snapshot will be encrypted
11. Stop the instance associated with the volume.
12. Detach the existing volume
13. attach the new volume to the recently available instance.
14. Start the instance.

## Risks

- Should ensure enough space on node. Chances of host giving 502 after attaching new volume is possible.

## Criteria For Success

- Attached volume is encrypted and site is accessible.

## Back-Out Plan

- When something is not working as expected we can revert back to previous storage volume.
