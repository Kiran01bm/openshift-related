# Federation Set-Up

## Federation Set-up
```
1. oc create ns kube-federation-system
2. kubefed operator deploy
3. Federate Clusters and Federate <Kinds>

~/Documents/GIT ⌚ 12:08:43
$ kubefedctl join cluster1 \
            --host-cluster-context cluster1 \
            --cluster-context cluster1 \
            --v=2
I0908 12:09:27.633480   65034 join.go:159] Args and flags: name cluster1, host: cluster1, host-system-namespace: kube-federation-system, kubeconfig: , cluster-context: cluster1, secret-name: , dry-run: false
I0908 12:09:34.409679   65034 join.go:238] Performing preflight checks.
I0908 12:09:34.438365   65034 join.go:244] Creating kube-federation-system namespace in joining cluster
I0908 12:09:34.467061   65034 join.go:377] Already existing kube-federation-system namespace
I0908 12:09:34.467087   65034 join.go:252] Created kube-federation-system namespace in joining cluster
I0908 12:09:34.467101   65034 join.go:398] Creating service account in joining cluster: cluster1
I0908 12:09:34.501254   65034 join.go:408] Created service account: cluster1-cluster1 in joining cluster: cluster1
I0908 12:09:34.501356   65034 join.go:436] Creating cluster role and binding for service account: cluster1-cluster1 in joining cluster: cluster1
I0908 12:09:34.623984   65034 join.go:445] Created cluster role and binding for service account: cluster1-cluster1 in joining cluster: cluster1
I0908 12:09:34.624034   65034 join.go:804] Creating cluster credentials secret in host cluster
I0908 12:09:34.708787   65034 join.go:830] Using secret named: cluster1-cluster1-token-f7642
I0908 12:09:34.740024   65034 join.go:873] Created secret in host cluster named: cluster1-28h6s
I0908 12:09:34.808889   65034 join.go:280] Created federated cluster resource

~/Documents/GIT   12:09:34
$ oc get kubefedclusters -n kube-federation-system
NAME       READY   AGE
cluster1           5s

~/Documents/GIT   12:09:39
$ kubefedctl join cluster2 \
            --host-cluster-context cluster1 \
            --cluster-context cluster2 \
            --v=2
I0908 12:09:50.557774   65046 join.go:159] Args and flags: name cluster2, host: cluster1, host-system-namespace: kube-federation-system, kubeconfig: , cluster-context: cluster2, secret-name: , dry-run: false
I0908 12:09:53.825846   65046 join.go:238] Performing preflight checks.
I0908 12:09:54.356455   65046 join.go:244] Creating kube-federation-system namespace in joining cluster
I0908 12:09:54.588735   65046 join.go:252] Created kube-federation-system namespace in joining cluster
I0908 12:09:54.588768   65046 join.go:398] Creating service account in joining cluster: cluster2
I0908 12:09:54.704458   65046 join.go:408] Created service account: cluster2-cluster1 in joining cluster: cluster2
I0908 12:09:54.704480   65046 join.go:436] Creating cluster role and binding for service account: cluster2-cluster1 in joining cluster: cluster2
I0908 12:09:55.145618   65046 join.go:445] Created cluster role and binding for service account: cluster2-cluster1 in joining cluster: cluster2
I0908 12:09:55.145664   65046 join.go:804] Creating cluster credentials secret in host cluster
I0908 12:09:55.471411   65046 join.go:830] Using secret named: cluster2-cluster1-token-7562c
I0908 12:09:55.498340   65046 join.go:873] Created secret in host cluster named: cluster2-6nzpx
I0908 12:09:55.563287   65046 join.go:280] Created federated cluster resource

~/Documents/GIT   12:09:55
$ oc get kubefedclusters -n kube-federation-system
NAME       READY   AGE
cluster1   True    24s
cluster2           3s
```

## Controller Logs - Active Instance
```

~/Documents/GIT ⌚ 10:11:23
$ cd /Users/kiran/Documents/demo/kubefed

~/Documents/demo/kubefed ⌚ 11:23:36
$ export KUBECONFIG=hostSydCluster/auth/kubeconfig

~/Documents/demo/kubefed ⌚ 11:23:50
$ oc config use-context cluster1
Switched to context "cluster1".

~/Documents/demo/kubefed ⌚ 11:24:00
$ oc config current-context
cluster1

~/Documents/demo/kubefed ⌚ 11:24:05
$ oc project kube-federation-system
Now using project "kube-federation-system" on server "https://api.kiran01bmhsyd.rhanzgps.net:6443".


~/Documents/demo/kubefed ⌚ 11:24:39
$ kubefedctl enable namespaces secrets serviceaccounts services configmaps deployments.apps scc
customresourcedefinition.apiextensions.k8s.io/federatednamespaces.types.kubefed.io created
federatedtypeconfig.core.kubefed.io/namespaces created in namespace kube-federation-system

~/Documents/demo/kubefed ⌚ 11:24:51
$ kubefedctl enable secrets
customresourcedefinition.apiextensions.k8s.io/federatedsecrets.types.kubefed.io created
federatedtypeconfig.core.kubefed.io/secrets created in namespace kube-federation-system

~/Documents/demo/kubefed ⌚ 11:25:09
$ kubefedctl enable serviceaccounts
customresourcedefinition.apiextensions.k8s.io/federatedserviceaccounts.types.kubefed.io created
federatedtypeconfig.core.kubefed.io/serviceaccounts created in namespace kube-federation-system

~/Documents/demo/kubefed ⌚ 11:25:22
$ kubefedctl enable services
customresourcedefinition.apiextensions.k8s.io/federatedservices.types.kubefed.io created
federatedtypeconfig.core.kubefed.io/services created in namespace kube-federation-system

~/Documents/demo/kubefed ⌚ 11:25:38
$ kubefedctl enable configmaps
customresourcedefinition.apiextensions.k8s.io/federatedconfigmaps.types.kubefed.io created
federatedtypeconfig.core.kubefed.io/configmaps created in namespace kube-federation-system

~/Documents/demo/kubefed ⌚ 11:25:52
$ kubefedctl enable deployments.apps
customresourcedefinition.apiextensions.k8s.io/federateddeployments.types.kubefed.io created
federatedtypeconfig.core.kubefed.io/deployments.apps created in namespace kube-federation-system

~/Documents/demo/kubefed ⌚ 11:26:03
$ kubefedctl enable scc
customresourcedefinition.apiextensions.k8s.io/federatedsecuritycontextconstraints.types.kubefed.io created
federatedtypeconfig.core.kubefed.io/securitycontextconstraints.security.openshift.io created in namespace kube-federation-system



This is the active Controller Logs showing sync controllers being spun-up as we federate a <KIND>
~/Documents/demo/kubefed ⌚ 11:24:13
$ oc logs -f kubefed-controller-manager-68fb5b6485-zcqts
KubeFed controller-manager version: version.Info{Version:"v0.1.0-rc6", GitCommit:"7586b42f4f477f1912caf28287fa2e0a7f68f407", GitTreeState:"clean", BuildDate:"2019-08-17T02:48:03Z", GoVersion:"go1.12.5", Compiler:"gc", Platform:"linux/amd64"}
W0908 01:19:42.413151       1 client_config.go:549] Neither --kubeconfig nor --master was specified.  Using the inClusterConfig.  This might not work.
I0908 01:19:42.815472       1 controller-manager.go:224] Setting Options with KubeFedConfig "kube-federation-system/kubefed"
I0908 01:19:42.815512       1 controller-manager.go:315] Using valid KubeFedConfig "kube-federation-system/kubefed"
I0908 01:19:42.815543       1 controller-manager.go:139] KubeFed will target all namespaces
I0908 01:19:42.818236       1 leaderelection.go:205] attempting to acquire leader lease  kube-federation-system/kubefed-controller-manager...
I0908 01:19:42.912720       1 leaderelection.go:214] successfully acquired lease kube-federation-system/kubefed-controller-manager
I0908 01:19:42.912953       1 leaderelection.go:75] promoted as leader
I0908 01:19:43.443845       1 controller.go:91] Starting cluster controller
I0908 01:19:43.820696       1 controller.go:70] Starting scheduling manager
I0908 01:19:45.307222       1 controller.go:88] Starting ServiceDNS controller
I0908 01:19:45.921033       1 controller.go:113] Starting "service" DNSEndpoint controller
I0908 01:19:46.021251       1 controller.go:124] "service" DNSEndpoint controller synced and ready
I0908 01:19:46.728589       1 controller.go:79] Starting IngressDNS controller
I0908 01:19:47.331831       1 controller.go:113] Starting "ingress" DNSEndpoint controller
I0908 01:19:47.432069       1 controller.go:124] "ingress" DNSEndpoint controller synced and ready
I0908 01:19:48.007079       1 controller.go:70] Starting FederatedTypeConfig controller






I0908 01:25:48.792470       1 controller.go:210] Reconciling all namespaced FederatedTypeConfig resources on finalizer update for "kube-federation-system/namespaces"
I0908 01:25:49.233353       1 controller.go:101] Starting sync controller for "FederatedNamespace"
I0908 01:25:49.233392       1 controller.go:330] Started sync controller for "FederatedNamespace"

I0908 01:26:07.229064       1 controller.go:101] Starting sync controller for "FederatedSecret"
I0908 01:26:07.229113       1 controller.go:330] Started sync controller for "FederatedSecret"

I0908 01:26:20.630384       1 controller.go:101] Starting sync controller for "FederatedServiceAccount"
I0908 01:26:20.630423       1 controller.go:330] Started sync controller for "FederatedServiceAccount"

I0908 01:26:36.206487       1 controller.go:101] Starting sync controller for "FederatedService"
I0908 01:26:36.206529       1 controller.go:330] Started sync controller for "FederatedService"

I0908 01:26:50.345771       1 controller.go:101] Starting sync controller for "FederatedConfigMap"
I0908 01:26:50.345808       1 controller.go:330] Started sync controller for "FederatedConfigMap"

I0908 01:27:00.200620       1 controller.go:179] Starting schedulingpreference controller for ReplicaSchedulingPreference
I0908 01:27:01.319203       1 controller.go:101] Starting sync controller for "FederatedDeployment"
I0908 01:27:01.319240       1 controller.go:330] Started sync controller for "FederatedDeployment"
I0908 01:27:01.407760       1 controller.go:362] refreshing sync controller for "deployments.apps"
I0908 01:27:01.407784       1 controller.go:354] Stopping controller for "deployments.apps"
I0908 01:27:01.821383       1 controller.go:81] Starting replicaschedulingpreferences controller
I0908 01:27:01.821420       1 controller.go:197] Starting plugin FederatedDeployment for ReplicaSchedulingPreference
I0908 01:27:02.623566       1 controller.go:101] Starting sync controller for "FederatedDeployment"
I0908 01:27:02.623600       1 controller.go:330] Started sync controller for "FederatedDeployment"
E0908 01:27:02.706823       1 controller.go:264] Could not update status fields of the CRD: "kube-federation-system/deployments.apps": Operation cannot be fulfilled on federatedtypeconfigs.core.kubefed.io "deployments.apps": the object has been modified; please apply your changes to the latest version and try again

I0908 01:27:37.526021       1 controller.go:101] Starting sync controller for "FederatedSecurityContextConstraints"
I0908 01:27:37.526060       1 controller.go:330] Started sync controller for "FederatedSecurityContextConstraints"
```

## Controller Logs - Standy Instance
```
~/Documents/demo/kubefed ⌚ 13:07:05
$ export KUBECONFIG=/Users/kiran/Documents/demo/kubefed/composed-kubeconfig

~/Documents/demo/kubefed ⌚ 13:07:14
$ oc config use-context cluster1
Switched to context "cluster1".

~/Documents/demo/kubefed ⌚ 13:07:18
$ oc project kube-federation-system
Now using project "kube-federation-system" on server "https://api.kiran01bmhsyd.rhanzgps.net:6443".

~/Documents/demo/kubefed ⌚ 13:07:25
$ oc get pods
NAME                                          READY   STATUS    RESTARTS   AGE
kubefed-controller-manager-68fb5b6485-5w7sv   1/1     Running   0          109m
kubefed-controller-manager-68fb5b6485-zcqts   1/1     Running   0          109m
kubefed-operator-6d55bbc5d4-c4vjs             1/1     Running   0          114m

~/Documents/demo/kubefed ⌚ 13:07:31
$ oc get pods -o wide
NAME                                          READY   STATUS    RESTARTS   AGE    IP            NODE                                              NOMINATED NODE   READINESS GATES
kubefed-controller-manager-68fb5b6485-5w7sv   1/1     Running   0          109m   10.129.2.8    ip-10-0-134-70.ap-southeast-2.compute.internal    <none>           <none>
kubefed-controller-manager-68fb5b6485-zcqts   1/1     Running   0          109m   10.128.2.10   ip-10-0-145-210.ap-southeast-2.compute.internal   <none>           <none>
kubefed-operator-6d55bbc5d4-c4vjs             1/1     Running   0          114m   10.128.2.9    ip-10-0-145-210.ap-southeast-2.compute.internal   <none>           <none>

~/Documents/demo/kubefed ⌚ 13:07:34
$ oc logs -f kubefed-controller-manager-68fb5b6485-5w7sv
KubeFed controller-manager version: version.Info{Version:"v0.1.0-rc6", GitCommit:"7586b42f4f477f1912caf28287fa2e0a7f68f407", GitTreeState:"clean", BuildDate:"2019-08-17T02:48:03Z", GoVersion:"go1.12.5", Compiler:"gc", Platform:"linux/amd64"}
W0908 01:19:42.440762       1 client_config.go:549] Neither --kubeconfig nor --master was specified.  Using the inClusterConfig.  This might not work.
I0908 01:19:42.774609       1 controller-manager.go:224] Setting Options with KubeFedConfig "kube-federation-system/kubefed"
I0908 01:19:42.774651       1 controller-manager.go:315] Using valid KubeFedConfig "kube-federation-system/kubefed"
I0908 01:19:42.835763       1 controller-manager.go:139] KubeFed will target all namespaces
I0908 01:19:42.839816       1 leaderelection.go:205] attempting to acquire leader lease  kube-federation-system/kubefed-controller-manager...
E0908 01:19:42.947832       1 leaderelection.go:274] error initially creating leader election record: configmaps "kubefed-controller-manager" already exists
```
