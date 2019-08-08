# Details related to Metrics sub-systems


## Hawkular:

1. Hawkular Metrics as a metrics engine which stores the data persistently in a Cassandra database.
2. We can access and manage metrics more directly, use the Hawkular Metrics API.
3. When accessing Hawkular Metrics from the API, you are only able to perform reads. Writing metrics is disabled by default.
4. If you want individual users to also be able to write metrics, you must set the openshift_metrics_hawkular_user_write_access variable to true.
5. It is recommended to use the default configuration and only have metrics enter the system via Heapster
6. Based on Jboss Operations Network - Java based and runs in Jboss EAP.


## Heapster:

1. The kubelet exposes metrics that can be collected and stored in back-ends by Heapster.
   Note: cAdvisor on node gathers CPU and memory information for pods and makes it available at /stats endpoint
2. Heapster retrieves a list of all nodes from the master server.
3. Then contacts each node individually through the /stats endpoint.
4. From there, Heapster scrapes the metrics for CPU, memory and network usage.
5. Hepaster then exports them into Hawkular and Hawkular stores the metrics into the Cassandra backend.
6. The storage volume metrics available on the kubelet are not available through the /stats endpoint, but are available through the /metrics endpoint.


## Cassandra:

1. Backend where metrics are stored.
2. Java Based Distributed Non-Relattional Time Series DB
3. Shared Nothing Architecture
4. Each Cassandra Pod will be backed by its own volume for data.
5. 3 copies for HA.
6. Amount of storage to use for each Cassandra volume depends not only on the expected cluster size (number of nodes and pods) but also on the resolution and duration of the time series for metrics.


## Horizontal Pod AutoScalers:

1. When Metrics is configured, CPU, memory and network-based metrics are viewable from the OpenShift Container Platform web console and are available for use by horizontal pod autoscalers.


## Authentication:

1. WEB - The Hawkular Metrics service authenticates the user against OpenShift Container Platform to determine if the user has access to the project it is trying to access. Hawkular Metrics accepts a bearer token from the client and verifies that token with the OpenShift Container Platform server using a SubjectAccessReview. If the user has proper read privileges for the project, they are allowed to read the metrics for that project.

2. API - When accessing the Hawkular Metrics API, you must pass a bearer token in the Authorization header.


## Authorization:

1. Hawkular Metrics is a multi-tenanted application. It is configured so that a project in OpenShift Container Platform corresponds to a tenant in Hawkular Metrics.
2. As such, when accessing metrics for a project named MyProject you must set the Hawkular-Tenant header to MyProject.
3. There is also a special tenant named _system which contains system level metrics. This requires either a cluster-reader or cluster-admin level privileges to access.


## Metrics on OpenShift Web Console:

1. The OpenShift Container Platform web console uses the data coming from the Hawkular Metrics service to display its graphs.
2. The URL for accessing the Hawkular Metrics service must be configured with the metricsPublicURL option in the master webconsole-config configmap file.

 
## React to Log Events:

1. Hawkular Alerts must be connected to the Aggregated Logging’s Elasticsearch to react on log events.

 
## Reference:

1. Hawkular Metrics Rest API - [accessing-metrics-using-hawkular-metrics ](https://github.com/openshift/origin-metrics/blob/master/docs/hawkular_metrics.adoc#accessing-metrics-using-hawkular-metrics)

 
## Known Issues:

### Scalability Issues with Heapster:

Testing found that the heapster metrics component is capable of handling up to 25,000 pods. If the amount of pods exceed that number, Heapster begins to fall behind in metrics processing, resulting in the possibility of metrics graphs no longer appearing. Work is ongoing to increase the number of pods that Heapster can gather metrics on, as well as upstream development of alternate metrics-gathering solutions.


### Hawkular and ElasticSearch Dependency:

By default, Hawkular tries to find Elasticsearch on its default place (namespace logging, pod logging-es) at every boot. If Aggregated Logging is installed after Hawkular, the Hawkular Metrics pod might need to be restarted in order to recognize the new Elasticsearch server.

OCP Platform installation by default installs Hawkular in openshift-infra before installing Logging project.


## Diagrams
![Alt text](metrics.png?raw=true "")
