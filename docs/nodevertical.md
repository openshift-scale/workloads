# NodeVertical Workload

The nodevertical workload playbook is `workloads/nodevertical.yml` and will run the nodevertical workload on your cluster.

NodeVertical workload is used to measure kubelet density for OpenShift. This means NodeVertical loads several nodes to max pods using cluster loader.

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

### WORKLOAD_JOB_PRIVILEGED
Default: `true`  
Enables/disables running the workload pod as privileged.

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
Maximum number of pods that NodeVertical will create across the nodes it has labeled. The number of pods will be calculated against this value by subtracting the non-terminated pod count across the labeled nodes. Example: 4 nodes each with 10 pods, Max pods set to 250 per node and NODEVERTICAL_MAXPODS is set to 1000, then nodevertical will spawn 960 pods ((4 x 250) - (4 * 10)).

### NODEVERTICAL_POD_IMAGE
Default: `gcr.io/google_containers/pause-amd64:3.0`  
Pod image to use for pods that are nodevertical pods.

### NODEVERTICAL_STEPSIZE
Default: `50`  
Number of pods for cluster loader will create before waiting for pods to become running.

### NODEVERTICAL_PAUSE
Default: `60`  
Period of time (in seconds) for cluster loader to pause after creating pods and waiting for them to be running.

## Smoke test variables

```
NODEVERTICAL_TEST_PREFIX=nodevertical_smoke
NODEVERTICAL_CLEANUP=true
NODEVERTICAL_BASENAME=nodevertical
NODEVERTICAL_MAXPODS=200
NODEVERTICAL_POD_IMAGE="gcr.io/google_containers/pause-amd64:3.0"
NODEVERTICAL_STEPSIZE=0
NODEVERTICAL_PAUSE=0
```
