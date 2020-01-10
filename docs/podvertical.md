# PodVertical Workload

The podvertical workload playbook is `workloads/podvertical.yml` and will run the podvertical workload on your cluster.

PodVertical workload's purpose is a cluster limits test with the goal of loading as many pods (as configured) in a single namespace as possible in a cluster.

Running from CLI:

```sh
$ cp workloads/inventory.example inventory
$ # Add orchestration host to inventory
$ # Edit vars in workloads/vars/podvertical.yml or define Environment vars (See below)
$ time ansible-playbook -vv -i inventory workloads/podvertical.yml
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
Enables/disables running the workload wrapped by pbench-user-benchmark. When enabled, pbench agents can then be enabled (`ENABLE_PBENCH_AGENTS`) for further instrumentation data and pbench-copy-results can be enabled (`ENABLE_PBENCH_COPY`) to export captured data for further analysis.

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

### PODVERTICAL_TEST_PREFIX
Default: `podvertical`  
Test to prefix the pbench results.

### PODVERTICAL_CLEANUP
Default: `true`  
Enables/disables cluster loader cleanup of this workload on completion.

### PODVERTICAL_BASENAME
Default: `podvertical`  
Basename used by cluster loader for the project(s) it creates.

### PODVERTICAL_MAXPODS
Default: `1000`  
Maximum number of pods that will be created by the podvertical workload. Typically much higher values are used than the default for large scale tests.

### PODVERTICAL_POD_IMAGE
Default: `gcr.io/google_containers/pause-amd64:3.0`  
Pod image to use for pods that are podvertical pods.

### PODVERTICAL_STEPSIZE
Default: `500`  
Number of pods for cluster loader will create before waiting for pods to become running.

### PODVERTICAL_PAUSE
Default: `30`  
Period of time (in seconds) for cluster loader to pause after creating pods and waiting for them to be running.

### PODVERTICAL_TS_TIMEOUT
Default: `600`  
Period of time (in seconds) that cluster loader will wait for pods to come up to "Running" state per tuningset before sleeping `PODVERTICAL_PAUSE`. The tuningset is determined by `PODVERTICAL_STEPSIZE` and `PODVERTICAL_PAUSE` thus if you have a very large stepsize you will need a greater period of time to allow the Pods to come to "Running" state. This value prevents waiting infinitely for Pods that would otherwise never come up.

### EXPECTED_PODVERTICAL_DURATION
Default: `600`  
Pass/fail criteria. Value to determine if PodVertical workload executed in duration expected.

## Smoke test variables

```
PODVERTICAL_TEST_PREFIX=podvertical_smoke
PODVERTICAL_CLEANUP=true
PODVERTICAL_BASENAME=podvertical
PODVERTICAL_MAXPODS=100
PODVERTICAL_POD_IMAGE="gcr.io/google_containers/pause-amd64:3.0"
PODVERTICAL_STEPSIZE=0
PODVERTICAL_PAUSE=0
```
