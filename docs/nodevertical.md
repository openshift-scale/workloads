# NodeVertical Workload

The NodeVertical workload playbook is `workloads/nodevertical.yml` and will run the NodeVertical workload on your cluster.

NodeVertical workload is used to measure kubelet density for OpenShift. This means NodeVertical loads several nodes to max Pods using cluster loader.

Requirements:
* Labeling Nodes

Running from CLI:

```sh
$ cp workloads/inventory.example inventory
$ # Add orchestration host to inventory
$ # Edit vars in workloads/vars/nodevertical.yml or define Environment vars (See below)
$ time ansible-playbook -vv -i inventory workloads/nodevertical.yml
```

## Environment variables

### ES_HOST
Default: ``
Elasticsearch server host address (currently used by snafu), set to index results from cluster loader

### ES_PORT
Default: ``
Elasticsearch server port (currently used by snafu), set to index results from cluster loader

### ES_INDEX_PREFIX
Default: `snafu`
Elasticsearch server index prefix (currently used by snafu)

### SNAFU_USER
Default: `scale-ci`
user running the tests, used for identifying test results.

### SNAFU_CLUSTER_NAME
Default: (defaults to the clustername in the machineset)
clustername on which the tests are running, used for identifying test results.

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
Default: `false`  
Enables/disables the node selector that places the workload job on the `workload` node.

### WORKLOAD_JOB_TAINT
Default: `false`  
Enables/disables the toleration on the workload job to permit the `workload` taint.

### WORKLOAD_JOB_PRIVILEGED
Default: `false`  
Enables/disables running the workload pod as privileged.

### KUBECONFIG_FILE
Default: `~/.kube/config`  
Location of kubeconfig on orchestration host.

### PBENCH_INSTRUMENTATION
Default: `false`  
Enables/disables running the workload wrapped by pbench-user-benchmark. When enabled, pbench agents can then be enabled (`ENABLE_PBENCH_AGENTS`) for further instrumentation data and pbench-copy-results can be enabled (`ENABLE_PBENCH_COPY`) to export captured data for further analysis. Also, we can export the results to the server without enabling (`ENABLE_PBENCH_AGENTS`)

### ENABLE_PBENCH_AGENTS
Default: `false`  
Enables/disables the collection of pbench data on the pbench agent Pods. These Pods are deployed by the tooling playbook.

### ENABLE_PBENCH_COPY
Default: `false`  
Enables/disables the copying of pbench data to a remote results server for further analysis.

### PBENCH_SSH_PRIVATE_KEY_FILE
Default: `~/.ssh/id_rsa`  
Location of ssh private key to authenticate to the pbench results server.

### PBENCH_SSH_PUBLIC_KEY_FILE
Default: `~/.ssh/id_rsa.pub`  
Location of the ssh public key to authenticate to the pbench results server.

### PBENCH_SERVER
Default: There is no public default.  
DNS address of the pbench results server.

### SCALE_CI_RESULTS_TOKEN
Default: There is no public default.  
Future use for pbench and prometheus scraper to place results into git repo that holds results data.

### JOB_COMPLETION_POLL_ATTEMPTS
Default: `360`  
Number of retries for Ansible to poll if the workload job has completed. Poll attempts delay 10s between polls with some additional time taken for each polling action depending on the orchestration host setup.

### AZURE_AUTH
Default: false
Set it to true when running OCP on Azure.

### AZURE_AUTH_FILE
Default: ''
Path to the Azure auth file - terraform.azure.auto.tfvars.json found in the openshift install dir on the orchestration host i.e scale-ci-deploy/scale-ci-azure/terraform.azure.auto.tfvars.json.

### NODEVERTICAL_NODE_COUNT
Default: `4`  
Number of nodes to apply the nodevertical label to.  This isolates the NodeVertical Pods to achieve kubelet density.  You will have to adjust the value here for clusters < 5 nodes.

### NODEVERTICAL_TEST_PREFIX
Default: `nodevertical`  
Test to prefix the pbench results.

### NODEVERTICAL_CLEANUP
Default: `true`  
Enables/disables cluster loader cleanup of this workload on completion.

### NODEVERTICAL_BASENAME
Default: `nodevertical`  
Basename used by cluster loader for the project(s) it creates.

### NODEVERTICAL_MAXPODS
Default: `1000`  
Maximum number of Pods that NodeVertical will create across the nodes it has labeled. The number of Pods will be calculated against this value by subtracting the non-terminated pod count across the labeled nodes. Example: 4 nodes each with 10 Pods, Max Pods set to 250 per node and NODEVERTICAL_MAXPODS is set to 1000, then NodeVertical will spawn 960 Pods ((4 x 250) - (4 * 10)).

### NODEVERTICAL_POD_IMAGE
Default: `gcr.io/google_containers/pause-amd64:3.0`  
Pod image to use for Pods that are NodeVertical Pods.

### NODEVERTICAL_STEPSIZE
Default: `50`  
Number of Pods for cluster loader will create before waiting for Pods to become running.

### NODEVERTICAL_PAUSE
Default: `60`  
Period of time (in seconds) for cluster loader to pause after creating Pods and waiting for them to be "Running" state.

### NODEVERTICAL_TS_TIMEOUT
Default: `180`  
Period of time (in seconds) that cluster loader will wait for pods to come up to "Running" state per tuningset before sleeping `NODEVERTICAL_PAUSE`. The tuningset is determined by `NODEVERTICAL_STEPSIZE` and `NODEVERTICAL_PAUSE` thus if you have a very large stepsize you will need a greater period of time to allow the Pods to come to "Running" state. This value prevents waiting infinitely for Pods that would otherwise never come up.

### EXPECTED_NODEVERTICAL_DURATION
Default: `600`  
Pass/fail criteria. Value to determine if NodeVertical workload executed in duration expected.

### NODEVERTICAL_HEAVY
Default: `false`
Trigger nodevertical heavy workload. Rather than deploy sleep pods throughout all labeled nodes, this workload deploys a two layer application composed by a PostgreSQL database pod and another pod wih simple application that is able to perform several operations and store a result in the database. [Heavy node vertical app](https://github.com/rsevilla87/perfApp)

### NODEVERTICAL_HEAVY_PROBE_ENDPOINT
Default: `/ready`
Readiness probe endpoint for the application deployed by the heavy nodevertical. The default `/ready` endpoint inserts a record with the current timestamp in the ts table of the DDBB. i.e. `INSERT INTO ts VALUES ('2006-01-02T15:04:05Z07:00')`

Be aware that using nodevertical heavy has an extra resource consumption compared with the standard workload,
the following capture provides an idea about how much resources are required to run it.

Results obtained from running 250 pods (client + database) with the default `/ready` endpoint.
```
root@ip-172-31-76-65: ~ # oc adm top nodes -l nodevertical=true
NAME                                        CPU(cores)   CPU%   MEMORY(bytes)   MEMORY%   
ip-10-0-143-79.us-west-2.compute.internal   755m         10%    8495Mi          28%       
ip-10-0-155-81.us-west-2.compute.internal   732m         9%     8810Mi          29%       
ip-10-0-166-82.us-west-2.compute.internal   753m         10%    9116Mi          30%  
```

### PPROF_COLLECT
Default: `false`
If you'd like to enable pprof profile data collection of kubeapiserver and prometheus through conprof(https://github.com/conprof/conprof).
Enabling this will create a few services to collect profiles from the apiserver pods and then create a conprof tarball in the pbench tarball

### NODEVERTICAL_HEAVY_PROBE_PERIOD
Default: `30`
Readiness probe period for the application deployed by the heavy nodevertical.

## Smoke test variables
```
NODEVERTICAL_NODE_COUNT=4
NODEVERTICAL_TEST_PREFIX=nodevertical_smoke
NODEVERTICAL_CLEANUP=true
NODEVERTICAL_BASENAME=nodevertical
NODEVERTICAL_MAXPODS=200
NODEVERTICAL_POD_IMAGE="gcr.io/google_containers/pause-amd64:3.0"
NODEVERTICAL_STEPSIZE=0
NODEVERTICAL_PAUSE=0
```
