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
Enables/disables the node selector that places the workload job on the `workload` node.

### WORKLOAD_JOB_TAINT
Default: `true`  
Enables/disables the toleration on the workload job to permit the `workload` taint.

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
Default: `1000`  
Number of retries for Ansible to poll if the workload job has completed. Poll attempts delay 10s between polls with some additional time taken for each polling action depending on the orchestration host setup.

### HTTP_TEST_SUFFIX
Default: `1router`  
A label to append to pbench directories and mb result directories.

### HTTP_TEST_LOAD_GENERATORS
Default: `1`  
How many workload generators to use.

### HTTP_TEST_LOAD_GENERATOR_NODES
Default: *empty*  
Load-generator nodes described by an extended regular expression (use "oc get nodes" node names). If unset/empty, do not pin the workload generators to any nodes.

### HTTP_TEST_APP_PROJECTS
Default: `10`  
Number of projects to create for each type of application (4 types currently).

### HTTP_TEST_APP_TEMPLATES
Default: `1`  
Number of templates to create per project.

### HTTP_TEST_RUNTIME
Default: `120`  
Run time of individual HTTP test iterations in seconds.

### HTTP_TEST_MB_RAMP_UP
Default: `0`  
Thread ramp-up time in seconds. Must be < HTTP_TEST_RUNTIME.

### HTTP_TEST_MB_DELAY
Default: `0`  
Maximum delay between client requests in ms. Can be a list of numbers separated by commas.

### HTTP_TEST_MB_TLS_SESSION_REUSE
Default: `true`  
Use TLS session reuse.

### HTTP_TEST_MB_METHOD
Default: `GET`  
HTTP method to use for backend servers (GET/POST).

### HTTP_TEST_MB_RESPONSE_SIZE
Default: `1024`  
Backend server (200 OK) response document length.

### HTTP_TEST_MB_REQUEST_BODY_SIZE
Default: `1024`  
Body length of POST requests in characters for backend servers.

### HTTP_TEST_ROUTE_TERMINATION
Default: `mix`  
Perform the test for the following (comma-separated) route terminations: mix,http,edge,passthrough,reencrypt.

### HTTP_TEST_SMOKE_TEST
Default: `false`  
Run only a single HTTP test to establish functionality. It also overrides HTTP_TEST_APP_PROJECTS and HTTP_TEST_APP_TEMPLATES.

### HTTP_TEST_NAMESPACE_CLEANUP
Default: `true`  
Delete all namespaces with application pods, services and routes created for the purposes of HTTP tests.

### HTTP_TEST_STRESS_CONTAINER_IMAGE
Default: `quay.io/openshift-scale/http-stress`  
HTTP workload generator container image.

### HTTP_TEST_SERVER_CONTAINER_IMAGE
Default: `quay.io/openshift-scale/nginx`  
HTTP server container image.

### ES_SERVER
Default: *empty*     
Public elasticsearch server.

### ES_PORT
Default: *empty*    
Port number for public elasticsearch server.

### COMPARE_RESULTS
Default: *empty*     
To compare two similar runs, export this variable.

### COMPARISON_TOLERANCE
Default: *empty*   
Accepted deviation in percentage when compared to a baseline run.

### BASELINE_RESULTS_TARBALL
Default: *empty*    
Link to tarball on a public instance which serves as a baseline for the test run. Pbench tarballs are internal to RHT, copy them to a public instance.

### EMAIL_ID_FOR_RESULTS_SHEET
Default: *empty*     
Email Id to which the results in google spreadsheet will be mailed

### GSHEET_KEY
Default: *empty*       
The service account key for communicating with Google API. 
Note: Please pad the key i.e " will be backslash" and \n will be backslash\n

## Suggested configurations

### Smoke test

HTTP smoke test is the shortest-running HTTP test that establishes test's basic functionality and the ability of the framework to upload results to the pbench results server.

```
PBENCH_SERVER=pbench.example.com # set to your own pbench server
ENABLE_PBENCH_AGENTS=false
HTTP_TEST_SMOKE_TEST=true
```

## Small-scale

A suggested configuration for running HTTP tests on a small-scale cluster with 4 worker nodes, each with 4 vCPUs, 16GB RAM. This configuration is used for documenting OpenShift's "Baseline router performance".

```
PBENCH_SERVER=pbench.example.com # set to your own pbench results server
ENABLE_PBENCH_AGENTS=true
#HTTP_TEST_LOAD_GENERATOR_NODES= # set when you have cluster administrator privileges
HTTP_TEST_APP_TEMPLATES=10
HTTP_TEST_ROUTE_TERMINATION=http,edge,passthrough,reencrypt
```

## Large-scale

A suggested configuration for running HTTP tests on a larger-scale cluster with 10+ worker nodes, each with 4 vCPUs, 16GB RAM. This configuration is used for router regression testing between OpenShift releases.

```
PBENCH_SERVER=pbench.example.com # set to your own pbench results server
ENABLE_PBENCH_AGENTS=true
#HTTP_TEST_LOAD_GENERATOR_NODES= # set when you have cluster administrator privileges
HTTP_TEST_APP_TEMPLATES=50
```
