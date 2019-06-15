# HTTP Workload

The HTTP workload playbook is `workloads/http.yml` and will run the HTTP(s) workload on your cluster.

The purpose of the HTTP workload is to test TPS and latency of the OpenShift Router/Ingress (HAProxy). The workload creates workload generator (WLG) pods and nginx pods to serve as endpoints. Traffic passes from the WLG to the nginx pods through OpenShift routes, thus stressing the ingress components of the OpenShift cluster.

Running from CLI:

```sh
$ cp workloads/inventory.example inventory
$ # Add orchestration host to inventory
$ # Edit vars in workloads/vars/http.yml or define Environment vars (See below)
$ time ansible-playbook -vv -i inventory workloads/http.yml
```

## Environment variables

### PUBLIC_KEY
Default: `~/.ssh/id_rsa.pub`  
Public ssh key file for Ansible.

### PRIVATE_KEY
Default: `~/.ssh/id_rsa`  
Private ssh key file for Ansible.

### ORCHESTRATION_USER
Default: `root`  
User for Ansible to log in as. Must authenticate with PUBLIC_KEY/PRIVATE_KEY.

### WORKLOAD_IMAGE
Default: `quay.io/openshift-scale/scale-ci-workload`  
Container image that runs the workload script.

### WORKLOAD_JOB_NODE_SELECTOR
Default: `true`  
Enables/disables the node selector that places the workload job on the `pbench` node.

### WORKLOAD_JOB_TAINT
Default: `true`  
Enables/disables the toleration on the workload job to permit the `controller` taint.

### KUBECONFIG_FILE
Default: `~/.kube/config`  
Location of kubeconfig on orchestration host.

### PBENCH_SSH_PRIVATE_KEY_FILE
Default: `~/.ssh/id_rsa`  
Location of ssh private key to authenticate to the pbench results server.

### PBENCH_SSH_PUBLIC_KEY_FILE
Default: `~/.ssh/id_rsa.pub`  
Location of the ssh public key to authenticate to the pbench results server.

### ENABLE_PBENCH_AGENTS
Default: `false`  
Enables/disables the collection of pbench data on the pbench agent pods. These pods are deployed by the tooling playbook.

### PBENCH_SERVER
Default: There is no public default.  
DNS address of the pbench results server.

### SCALE_CI_RESULTS_TOKEN
Default: There is no public default.  
Future use for pbench and prometheus scraper to place results into git repo that holds results data.

### JOB_COMPLETION_POLL_ATTEMPTS
Default: `360`  
Number of retries for Ansible to poll if the workload job has completed. Poll attempts delay 10s between polls with some additional time taken for each polling action depending on the orchestration host setup.

### TEST_CFG
Default: `1router`  
A label to append to pbench directories and mb result directories.

### PBENCH_USE
Default: `false`  
Use benchmarking and performance analysis framework pbench.

### PBENCH_SCRAPER_USE
Default: `false`  
Pbench scraper/wedge usage for r2r analysis.

### CLEAR_RESULTS
Default: `true`  
Clear results from previous pbench runs.

### MOVE_RESULTS
Default: `false`  
Move the benchmark results to a pbench server.

### SERVER_RESULTS
Default: No Default  
Endpoint for collecting results from workload generator node(s). Keep empty if copying is not required.

### SERVER_RESULTS_SSH_KEY
Default: `/root/.ssh/id_rsa`  
Path to private key when using scp:// to copy results to SERVER_RESULTS. It is mounted to workload generator container(s).

### LOAD_GENERATORS
Default: `1`  
How many workload generators to use.

### LOAD_GENERATOR_NODES
Default: No Default  
Load-generator nodes described by an extended regular expression (use "oc get nodes" node names). If unset/empty, do not pin the workload generators to any nodes.

### CL_PROJECTS
Default: `10`  
Number of projects to create for each type of application (4 types currently).

### CL_TEMPLATES
Default: `1`  
Number of templates to create per project.

### RUN_TIME
Default: `120`  
Run time of individual HTTP test iterations in seconds.

### MB_DELAY
Default: `0`  
Maximum delay between client requests in ms.

### MB_TLS_SESSION_REUSE
Default: `true`  
Use TLS session reuse.

### MB_METHOD
Default: `GET`  
HTTP method to use for backend servers (GET/POST).

### MB_RESPONSE_SIZE
Default: `1024`  
Backend server (200 OK) response document length.

### MB_REQUEST_BODY_SIZE
Default: `1024`  
Body length of POST requests in characters for backend servers.

### ROUTE_TERMINATION
Default: `mix`  
Perform the test for the following (comma-separated) route terminations: mix,http,edge,passthrough,reencrypt.

### SMOKE_TEST
Default: `false`  
Run only a single HTTP test to establish functionality. It also overrides CL_PROJECTS and CL_TEMPLATES.

### NAMESPACE_CLEANUP
Default: `true`  
Delete all namespaces with application pods, services and routes created for the purposes of HTTP tests.

### HTTP_STRESS_CONTAINER_IMAGE
Default: `quay.io/openshift-scale/http-stress`  
HTTP workload generator container image.

### HTTP_SERVER_CONTAINER_IMAGE
Default: `quay.io/openshift-scale/nginx`  
HTTP server container image.

## Smoke test variables

```
TEST_CFG=1router_smoke
PBENCH_USE=false
PBENCH_SCRAPER_USE=false
CLEAR_RESULTS=true
MOVE_RESULTS=true
# SERVER_RESULTS
SERVER_RESULTS_SSH_KEY="/root/.ssh/id_rsa"
LOAD_GENERATORS=1
# LOAD_GENERATOR_NODES
CL_PROJECTS=2
CL_TEMPLATES=1
RUN_TIME=120
MB_DELAY=0
MB_TLS_SESSION_REUSE=true
MB_METHOD=GET
MB_RESPONSE_SIZE=1024
MB_REQUEST_BODY_SIZE=1024
ROUTE_TERMINATION=mix
SMOKE_TEST=true
NAMESPACE_CLEANUP=true
HTTP_STRESS_CONTAINER_IMAGE="quay.io/openshift-scale/http-stress"
HTTP_SERVER_CONTAINER_IMAGE="quay.io/openshift-scale/nginx"
```
