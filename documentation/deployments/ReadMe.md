## General Deployment Related Notes

## DC related topic areas
1. Deployment Trigger
2. Strategy to move from older version to newer version of the app
3. RC config
4. Rollback

## Deployment Strategies
2 Deployment strategies i.e using Deployment Config and other using the Routes. 
If we use DC then all routes used by the App are impacted but if we use Route strategy only the said Route is going to be impacted..

## DC Based:
1. Rolling - Default option if none mentioned. And in Openshift it is Canary
This strategy uses readiness probes to progressively rollout a new version of App. An instance of older version of the app wont be scaled-down until an instance of newer version of the App is ready.
2. Recreate
Stop all instances of the application and then rollout a new application.
3. Custom
DIY

## Life cycle hooks

Life cycle hooks run in separate short-lived containers just like Build and Deploy pods and are cleaned-up after the task is complete.

Recreate and Rolling strategies support Pre, Mid and Post lifecycle hooks.

Automatic DB initialisation and DB migration are good use-cases for these Lifecycle hooks.

1. Pre - Before shutdown of old pods and start-up of any new pods
2. Mid - After shutdown of old pods and before start-up of new pods
3. Post - After start-up of new pods and shut-down of old pods

Each hook has a failure policy i.e which defines what is to be done when the hook fails:
1. Abort - Abort the deployment process and mark the deployment as failed.
2. Retry - Retry the hook until it succeeds.
3. Ignore - Ignore hook failure and allow deployment to complete

## Route Based:
1. Blue-Green
2 versions of the App and Router used to point to a chosen version of the Application. So 1 route and 2 services. 1 service for Blue and the other for Green.
2. A/B 
Allows a new version of the Application to be deployed to a subset of users. 

**NOTE:** N and N-1 Compatibility at data layer (Database, Shared Cache etc) will require planning at Application design and not a concern thats just handled during Deployment. This is important as most of the deployment strategies has the older version and newer version of the app running if we choose a 0 downtime approach.

## Application Shutdown during deployment:
Openshift terminates pods and waits for Graceful shutdown of the app. App must handle graceful termination of user connections.

OCP sends **TERM signal - SIGTERM** to the processes in a Conatiner and the Application is expected to stop accepting new connections so that router can send new connections to other versions of the application and gracefully handle live connections till completion. If the graceful termination period has expired then Openshift sends a **KILL signal - SIGKILL** to any process that had not exited. The **terminationGracePeriodSeconds** attribute is part of the Pod template and the default is 30 seconds. 

## Rollback

Rollback Example:
```
$ oc rollout undo dc/<name> - The DC template will be reverted to match the deployment revision specified in the undo command, and a new replication controller will be started.
```

Image change triggers on the DC are disabled as part of the rollback to prevent accidentally starting a new deployment process soon after the rollback is complete. To re-enable the image change triggers:
```
$ oc set triggers dc/<name> --auto
```

Redeploy example:
```
$ oc rollout retry dc/registry-console
```
**Note:** OCP has a feature to roll back to the last successfully deployed rc in case of a failure
