## General Notes

1. OpenShift is supported anywhere that Red Hat Enterprise Linux is. Only x86 platforms are supported

2. The main value that OpenShift adds to Docker + Kubernetes is automated development workflows and supporting subsystems. Ex: S2I, CI/CD, IDE Integration, EFK, Prometheus, Registry, Metrics,  etc etc

3. Kubernetes namespace provide a mechanism to scope cluster resources. OpenShift Project is a collection of Resources enabling RBAC and Traffic control to be built into them.

4. Services provide **stable network identity**. Naming convention - $(service name).$(namespace).svc.cluster.local

5. Routes **bypass** the service networks and access pods directly. Unlike services, which use selectors to link to pod resources containing specific labels, routes link directly to the service resource name. The router queries the named Service for endpoints and registers valid endpoints for load-balancing. 

6. DC also has the provision to define strategy to transition from current version to the new version. The Deployer POD manages the deployment i.e Deployer Pod implements the deployment Logic (calling lifecyle hooks, transitioning replication controllers and Tracking Pods).

7. OCP Routers Support HTTPS with SNI and TLS with SNI.

8. oc new-app creates application by specifying source code, image, and/or template. New app creation workflow/logic is implemented by the **Builder Pod**
```
oc new-app with docker image does not create BC - It creates DC, Service, IS
oc new-app with source code creates - BC, DC< Service, IS
```

9. For Quotas (be it single project Quota or Multiproject quota defined by a ClusterResourceQuota) to be effective, you must create Limit Ranges which handles the Default problem very well
```
Both LimitRange and ResourceQuota are Project Scoped.

OpenShift manages quotas for the use of objects and compute resources in a cluster by using a ResourceQuota object, or simply a quota. The ResourceQuota object specifies hard resource usage limits for a project. All attributes of a quota are optional, meaning that any resource that is not restricted by a quota can be consumed without bounds.

Quota attributes can track either the resource requests or the resource limits for all pods in the project. By default, quota attributes tracks resource requests. To track resource limits instead, prefix the compute resource name with limits, for example, limits.cpu.

ResourceQuota constraints are applied for the project as a whole.

Object-Quota and Compute-Quota enforcement:
If a modification to a project exceeds the quota for an object count, the action is denied by the server, and an appropriate error message is returned to the user. If the modification exceeds
the quota for a compute resource, however, the operation does not fail immediately; OpenShift retries the operation several times, giving the administrator an opportunity to increase the quota or to perform another corrective action, such as bringing a new node online.

Limit Range:
A LimitRange resource, also called a limit, defines the default, minimum, and maximum values for compute resource requests and limits for a single pod or for a single container defined inside the project. A resource request or limit for a pod is the sum of its containers.

To understand the difference between a limit range and a resource quota resource, consider that a limit range defines valid ranges and default values for a single pod, while a resource quota defines only top values for the sum of all pods in a project. A cluster administrator concerned about resource usage in an OpenShift cluster usually defines both limits and quotas for a project.

Avoid setting LimitRange constraints that are too high, or ResourceQuota constraints that are too low. Violation of LimitRange constraints prevents pod from being created, showing clear error messages. Violation of ResourceQuota constraints prevents a pod from being scheduled to any node. The pod might be created but remain in the pending state.

ClusterResourceQuota aka MultiProject Quota:
It is not recommended to have a single cluster resource quota that matches over a hundred projects. This is to avoid large locking overheads. When resources in a project are created or updated, the project is locked while searching for all applicable resource quotas.
```

10. When you **delete the StatefulSet**, it does not touch the persistent volumes nor the service, so we have to take care of that ourselves
```
    StatefulSet Pods have a stable and unique identity that is comprised of an ordinal, a stable network identity, and stable storage. 
```
11. CPU requests you set in Kubernetes or OpenShift is a CPU-Feature agnostic setting i.e 200 millicores of a faster processor means higher computational speed and throughput

12. Master - API Server, Controlloer Server Manager, Scheduler, Etcd

13. All of the Master components except API server works in a Leader Election mode with one Leader and many followers. API Server is stateless and runs in a Active-Active manner

14. **S2I** relies on ONBUILD Docker instruction.

15. **Authorization evaluation** - Cluster-wide allow rules checked --> Locally bound allow rules checked --> Deny by default

16. Service account naming convention - system:serviceaccount:<project>:<name>

17. Every service account is part of the following groups - system:serviceaccounts: and system:serviceaccounts:<project>:

18. OpenShift master includes **OAUTH** server which implements OAUTH2.0 but not OIDC. It uses the Authorization Code Flow.

19.  FluentD runs as a **Privileged Container** with HOSTPATH PV's

20. User can **impersonate** another user or a service account if the authorization to impersonate is granted

21. **Liveness Probe** - Important in scenarios where the Container is UP but not doing anything worthwhile

22. **Readiness Probe** - Used by various Controllers to determine if they can be added to the pool of active workers.

23. Failing to disable **swap** results in nodes not recognizing that they are experiencing MemoryPressure, resulting in pods not receiving the memory they made in their scheduling request. As a result, additional pods are placed on the node to further increase memory pressure, ultimately increasing your risk of experiencing a system out of memory (OOM) event.

24. **Static pods** are managed directly by kubelet/node service daemon on a specific node, without API server observing it.

25. Quality of Service **(QoS)** classes: For the sake of brevity, suffice it to say that there is a complex and powerful system of Quality of Service and resource management in OpenShift. Understanding the types of workloads that will be run in your cluster will be important to coming up with sensible values for all of these settings.
```
	Guaranteed - If limits and optionally requests are set (not equal to 0) for all resources and they are equal, then the container is classified as Guaranteed.
	Burstable - If requests and optionally limits are set (not equal to 0) for all resources, and they are not equal, then the container is classified as Burstable.
	BestEffort - If requests and limits are not set for any of the resources, then the container is classified as BestEffort.
```
26. **DaemonSets** should not create **BestEffort** pods to avoid being identified as a candidate pod for eviction. Instead DaemonSets should ideally launch Guaranteed pods.

27. Memory is an incompressible resource, so in low memory situations, containers that have the lowest priority are killed first.

28. Controlling **Node Condition Oscillation** - If a node is oscillating above and below a soft eviction threshold, but not exceeding its associated grace period, the corresponding node condition oscillates between true and false, which can cause problems for the scheduler.
	Example:
		kubeletArguments:
  		  eviction-pressure-transition-period="5m"

29. **Node Resource Reservation**: [Allocatable] = [Node Capacity] - [kube-reserved] - [system-reserved] - [Hard-Eviction-Thresholds]

30. **Overcommitment** - Some applications lend themselves well to overcommitted environments, and some do not. ex: Most Java applications and applications that use huge pages are examples of applications that would not allow for overcommitment. That memory can not be used for other applications.

31. Understanding **Pod Eviction:** Pretty comprehensive and for details refer the documentation.

32. Understanding Quality of Service and Out of Memory Killer: Pretty comprehensive and for details refer the documentation.

33. Kubernetes Deployments v/s Openshift Deployment Configs: Given the current feature set of Kubernetes deployments, you may want to use them instead of deployment configurations in OpenShift Container Platform if you do not plan to use any of the following in particular:
	* image streams
	* lifecycle hooks
	* Custom deployment strategies

34. Deployment Trigger
```
	1 - Config Change - When Pod Template changes (or , i.e RC template of the DC changes) new RC will be created and the deployment POD handles the logic of rolling out new version of the application i.e scaling down old RC and scaling up the new RC 

		* Revision history limit is the limit of old replication controllers you want to keep around for rolling back. May be omitted. If omitted, old replication controllers will not be cleaned up.

	2 - ImageChange Trigger - The ImageChange trigger results in a new replication controller whenever the content of an image stream tag changes (when a new version of the image is pushed).

		* When the tag value of an image stream changes new RC will be created
```

35. **Graceful Termination** - On shutdown, OpenShift Container Platform will send a *TERM* signal to the processes in the container. Application code, on receiving SIGTERM, should stop accepting new connections. This will ensure that load balancers route traffic to other active instances. The application code should then wait until all open connections are closed (or gracefully terminate individual connections at the next opportunity) before exiting. After the graceful termination period expires, a process that has not exited will be sent the *KILL* signal, which immediately ends the process. The terminationGracePeriodSeconds attribute of a pod or pod template controls the graceful termination period (default 30 seconds) and may be customized per application as necessary.

36. Deployment Strategies - **2 Deployment strategies** i.e using Deployment Config and other using the Routes. If we use DC then all routes used by the App are impacted but if we use Route strategy only the said Route is going to be impacted.
```
DC Based:
	Rolling - Default option if none mentioned. And in Openshift it is Canary
		This strategy uses readiness probes to progressively rollout a new version of App. An instance of older version of the app wont be scaled-down until an instance of newer version of the App is ready.
	Recreate
		Stop all instances of the application and then rollout a new application.
	Custom
		DIY

	Recreate and Rolling strategies support Pre, Mid and Post lifecycle hooks. Automatic DB initialisation and DB migration are good use-cases for these Lifecycle hooks. Life cycle hooks run in separate short-lived containers just like Build and Deploy pods and are cleaned-up after the task is complete.
		* Pre - Before shutdown of old pods and start-up of any new pods
		* Mid - After shutdown of old pods and before start-up of new pods
		* Post - After start-up of new pods and shut-down of old pods
	Each hook has a failure policy i.e which defines what is to be done when the hook fails:
		* Abort - Abort the deployment process and mark the deployment as failed.
		* Retry - Retry the hook until it succeeds.
		* Ignore - Ignore hook failure and allow deployment to complete

Route Based:
	Blue-Green
		2 versions of the App and Router used to point to a chosen version of the Application. So 1 route and 2 services. 1 service for Blue and the other for Green.
	A/B 
		Allows a new version of the Application to be deployed to a subset of users. 

	N and N-1 Compatibility at data layer (Database, Shared Cache etc) will require planning at Application design and not a concern thats just handled during Deployment. This is important as most of the deployment strategies has the older version and newer version of the app running if we choose a 0 downtime approach.
```

37. Build Related
```
BC has 2 main inputs: Most commonly used configuration is GIT for Build Input Source and Source or Docker for the Build Strategy.
	- Build Input Source:
		* GIT
		or
		* Binary
		* 4 more with one of the being a way to pass secrets for the build and these secrets will not be part of the final image that is built.
	
	- Build Strategy:
		* Source - Based on S2I process. Developer need not know the lowlevel stuff to write Dockerfiles.
		* Docker - Openshift runs the docker build and the strategy requires a GIT repo with a Dockerfile and the build artifacts.
		* Pipeline - Uses Jenkins where Build pipelines are triggered and managed by Openshift. OCP creates the Jenkins server on 1st trigger and reuses it for future builds.
		* Custom - Here you specify a custom builder image.

	- Build Triggers: oc new-app
		* Image change trigger - Triggers when parent image changes. If a registry does not support notification then prediodically run "oc import-image"
		* Config change trigger - When build config changes it starts new application builds
		* Webhook triggers - Can be triggered from source ex: Commit on Source Code repo
		* Generic/Custom

	- Webhook Triggers:
		- Supports the following types of SCM's
			- Github
			- Gitlab
			- Bitbucket
```

38. oc new-app related
```
*Source can be on* - Local Machine as a bunch of files in a directory, Local Git Repo, Remote GIT Repo.
*Build Strategy* - Can be Docker or S2I, If Dockerfile is found and no strategy specified then Docker strategy is used. If Builder is not specified then its worked out based on the source checks.
*Deployment* can be done by new-app by specifying a Docker image be it in Local or Integrated Registry or External Registry

oc new-app Related: Also refer to the activity diagram
	oc new-app can detect the source programming language. If detection fails or yeilds an incorrect result then there are a number of disambiguation parameters. We can even feed a template to create a new-app and this template should contain a definition for BuildConfig

	oc new-app creates application by specifying source code, image, and/or template.

	oc new-app creates: ex: oc new-app --docker-image=registry/repo/image:tag --name=NewApp
		- BC - to build application container image from either source code or docker file
		- DC --> RC, dc, rc
		- Services, svc
		- Imagestream, is - Points to a generated image either in the internal registry or to an image in an external registry. BC and DC use IS events to trigger S2I builds or deployments of new images.
		- Pod, pod

		- Build Config --> Build --> Image Streams
		- Deployment Config --> Deployment --> RC --> Service --> Pod

		Other resources like the below can be created either before or after the oc new-app command:
		- Routes
		- Secrets 
		- Configmaps 
		- PVC's

		There is also a dry run mode to execute the command which prints out the resources created by the oc new-app in a JSON or YAML format.

		Environment Variables:
			- DC stores env variables for Application Pods ex: oc new-app -e key=value. These values are stored in the DC and added to all pods created during the deployment.
			- BC stores env variables for Builder pods ex: oc new-app --build-env key=value
			- S2I image builders also have environment variables to avoid baking in configuration.

		oc start-build for BC is same as oc rollout for DC

	oc new-app creates all the resources and adds a lable name "app=appname" to all the resources it creates and this can be queried as below:
		~/Documents ⌚ 10:50:06
				$ oc get all -l app=nodejs-ex
				NAME                          REVISION   DESIRED   CURRENT   TRIGGERED BY
				deploymentconfigs/nodejs-ex   1          1         1         config,image(nodejs-ex:latest)

				NAME                     TYPE      FROM      LATEST
				buildconfigs/nodejs-ex   Source    Git       1

				NAME                 TYPE      FROM          STATUS     STARTED      DURATION
				builds/nodejs-ex-1   Source    Git@e79929f   Complete   7 days ago   1m13s

				NAME                     DOCKER REPO                           TAGS      UPDATED
				imagestreams/nodejs-ex   172.30.1.1:5000/myproject/nodejs-ex   latest    7 days ago

				NAME               HOST/PORT                                 PATH      SERVICES    PORT       TERMINATION   WILDCARD
				routes/nodejs-ex   nodejs-ex-myproject.192.168.64.2.nip.io             nodejs-ex   8080-tcp                 None

				NAME                   READY     STATUS    RESTARTS   AGE
				po/nodejs-ex-1-w4gtq   1/1       Running   1          7d

				NAME             DESIRED   CURRENT   READY     AGE
				rc/nodejs-ex-1   1         1         1         7d

				NAME            TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
				svc/nodejs-ex   ClusterIP   172.30.186.204   <none>        8080/TCP   7d

		~/Documents ⌚ 10:50:23
		$

	New app specifiying the nodejs s2i builder image that needs to be used for building this app.
		oc new-app nodejs:8~GITURL

	Build from a Local GIT repo: The local repo should have a remote/origin pointing to a URL accessible to openshift
		oc new-app .

	Use the below to build from a branch:
		oc new-app GITURL#BRANCHNAME

	Output the resource definitions that will be created
		oc new-app -o json GITURL --name SAMPLEAPP > sampleapp_s2i.json

	New App from an existing image
		oc new-app my-image-stream:v1

	By default a route is not created when oc new-app is executed. However when we deploy via the console a route gets created as the deployment happens via the template.

```
39. Kubernetes API server can be extended with CRD's

40. Each container within a pod runs it's own cgroup, but shares IPC, Network, and UTC (hostname) namespaces

41. Default Service Accounts in a new Namespace - builder, deployer, default

42. Privileged Containers - ex: Registry Pod, Builder Pods (The S2I builder containers require access to the host docker daemon to build and run containers.)

43. Find an SCC that will run a Pod
```
oc export pod PODNAME -o yaml | oc adm policy scc-subject-review -f -
```
44. Like *oc rsh*: The *oc port-forward* command allows a developer's workstation to connect to network services inside a pod, even without any services or routes configured.

45. Creating a Horizontal Pod Autoscaler
```
oc autoscale dc/myapp --min 1 --max 10 --cpu-percent=80
```
46. Custom Service Account
```
oc create serviceaccount service-account-name
oc adm policy add-scc-to-user SCC -z service-account
edit pod to use this service account
```
47. The oc patch command has the advantage of being scriptable, but the oc edit command is easier for interactive use.

48. Supplemental Groups: When a process runs in Linux, it has a UID, a GID, and one or more supplemental groups. These attributes can be set for a container’s main process. 
```
The supplemental group IDs are typically used for controlling access to shared storage, such as NFS and GlusterFS, whereas fsGroup is used for controlling access to block storage, such as Ceph RBD and iSCSI

OpenShift shared storage plug-ins mount volumes such that the POSIX permissions on the mount match the permissions on the target storage. For example, if the target storage’s owner ID is 1234 and its group ID is 5678, then the mount on the host node and in the container will have those same IDs. Therefore, the container’s main process must match one or both of those IDs in order to access the volume.
```
49. User Types
```
User Types:
	Normal Users
	System Users - system:admin, system:openshift-registry, and system:node:node1.example.com - anonymous system user
	Service Accounts - system:serviceaccount:foo:builder, system:serviceaccount:default:deployer
```
50. User Creation: The default configuration for the Red Hat OpenShift Container Platform is to create new users automatically when they first log in. If the user credentials are accepted by the identity provider, OpenShift creates the user object.

51. DenyAllPasswordIdentityProvider: The OpenShift installer uses a secure by default approach, where DenyAllPasswordIdentityProvider is the default provider. Using this provider, only the local root user on a master machine can use OpenShift client commands and APIs.

52. Getting Traffic into Cluster
```
1. Routes - only http, https with SNI, tls with SNI and websockets
2. NodePort - Can be TCP or UDP
	Port numbers for NodePort attributes are restricted to the range 30000-32767 by default. This range is configurable in the OpenShift master configuration file.
	The node port is open on all the nodes in the cluster, including the master. If the node port value is not provided, OpenShift assigns a random port in the configured range automatically.
3. HostNetwork Pods like the Router Pods and requires higher privileges to run the Pod.
```
53. Node Maintenance - Drain.
```
1. Nodes must first be marked unschedulable to perform pod evacuation. Cordoning the node ie patching the node with node.Spec.Unschedulable=true
2. Only pods backed by a replication controller can be evacuated; the replication controllers create new pods on other nodes and remove the existing pods from the specified node(s). 
3. Bare pods, meaning those not backed by a replication controller, are unaffected by default. 
4. You can evacuate a subset of pods by specifying a pod-selector. 
5. Statefulsets - Lets say you have a 3 zone HA Cluster with one copy of stateful pod per AZ deployed (ex: ES where data is replicated at Application level and at Storage level its independent disks) there is no need to evacuate such pods.
```
54. The DaemonSet controller ignores unschedulable markings, In case of a Drain - A pod that belongs to a DaemonSet will be immediately replaced. 
55. Mirror pods are merely the corresponding read-only API resources of static pods - pods that are managed by the Kubelet, directly, without the API server managing them. Mirror pods are visible from the API server but cannot be controlled, so drain won’t delete these either.
56. Pods not managed by Controllers are called as Bare pods. Controllers which manage pods life cycle are listed below. The k8s apimachinery package has a util function that returns the controller for a pod, or nil, if there’s no controller for it: metav1.GetControllerOf(&pod)
```
Ex: ReplicationController, ReplicaSet, Job, DaemonSet or StatefulSet
```

57. Image Pre-reqs ie making an Image OpenShift compatible.
```
Image Pre-requisites: To build an image that can be run on Openshift.. Adapting docker files to run on Openshift.
	The user account that runs a container is always a arbitrary NON-ROOT user and part of ROOT group with an arbitrary user ID. The root group does not have special permissions like a root user which minimizes the security risks.
	Files are Folders in the that is expected to be read on written by the processes in the container should be owned by root group and have group RW permission and executables must have +X for Root Group.
	Having the below RUN instructyion in your Dockerfile will build your image to be Openshift ready
		ex: RUN chgrp -R 0 DIR && chmod -R g=u DIR or (chmod -R g+rwX DIR) -- This is to allow users in the root group to access the files ie RWX
	Processes running in the container should not listen on priveleged ports as the processes are not running as priveleged users.

	* g=u makes the groups permission same as the user/owner permission.

Running Containers as root user or UID=0 using SCC (Security Context Constraints): SCC = anyuid
	All containers created by Openshift runs with a a default scc i.e "restricted" which ignores the USER instruction set in the container and runs the container as a random non-root user.
	To allow the container to run as flexible users we need to run with SCC set to "anyuid". To do this:
		- Create a new service account and add it to an appropriate SCC and then change the DC to use this Service Account
			* oc create serviceaccount ACCOUNTNAME
			* oc adm policy add-scc-to-user anyuid -z SERVICEACCOUNT_NAME (SCC control what actions a Pod can perform on which resources.)
			* oc patch dc/DCNAME --patch '{"spec": {"template":{"spec":{"serviceaccountname" : "SERVICEACCOUNT_NAME"}}}}'
		- Service account is the identity for a Pod on OCP.
		- All Pods run with a default service account unless the Pod or its DC is configured otherwise.
	
SCC control what actions a Pod can perform on which resources. You can also use security context constraints to control the actions that a pod can perform and what it has the ability to access. 
```
58. By default, the cluster installation process automatically creates Default Image Streams and Quick Start Templates in the openshift project, which is a default project to which all users have view access.
59. NFS based fileshares must have the following:
```
a. Owned by the nfsnobody user and group - As pods run with a random user id
b. Having rwx------ permissions (expressed as 0700 using octal).
c. Exported using the all_squash option.
```
59. Scheduler configuration file
```
/etc/origin/master/scheduler.json
```
60. Default node selector at namespace level ex: default namespace to target workloads to Infra nodes.To configure a default node selector for a project, add an annotation to the namespace resource with the openshift.io/node-selector key.
```
oc annotate --overwrite namespace default \ openshift.io/node-selector='region=infra'
```
61. Roles related to registry
```
system:registry
This role allows a user to pull images from the internal registry.

system:image-builder
This role allows a user to push images to the internal registry.

system:image-puller
If the application pod and the container image are in the same project, permission is automatically granted. However, if they are in different projects, the service accounts in the application project require the system:image-puller role from the container image project.

oc policy add-role-to-group -n image_project system:image-puller \ system:serviceaccounts:projectname

```
62. Build and Deployment hooks
```
Build Hook: Post commit build hook is executed after a build has committed an image but before the image has been pushed to the registry. This hook can be used to run tests before its available in the registry for consumption or before triggering the image change triggers.

Deployment Hook: Pre, Mid and Post.

```
63. Building custom base images
```
To build our own custom container image, we can start with a base operating system image (such as rhel7). To build and run applications requiring certain development tools and runtime libraries, Red Hat SCL provides a number of container images, featuring tools such as Node.js (rhscl/nodejs-6- rhel7), Ruby on Rails (rhscl/ror-42-rhel7), and Python (rhscl/python-35-rhel7).
```
64. Logs from Containers
```
The container runtime collects the standard output of the containers inside a pod and stores them as pod logs.
If a containerized application saves its log events to files, either in ephemeral container storage or a persistent volume, those logs are not displayed by the web console, nor by the oc logs command.
``
