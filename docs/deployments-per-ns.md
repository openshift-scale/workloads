# Deployments per Namespace Cluster Limits Workload

The deployments per namespace cluster limits workload playbook is `workloads/cluster-limits-deployments-per-ns.yml` and will run the deployments per namespace cluster limits test on your cluster.

Running from CLI:

```sh
$ cp workloads/inventory.example inventory
$ # Add orchestration host to inventory
$ # Edit vars in workloads/vars/deployments-per-ns.yml or define Environment vars (See below)
$ time ansible-playbook -vv -i inventory workloads/cluster-limits-deployments-per-ns.yml
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

### AZURE_AUTH
Default: false
Set it to true when running OCP on Azure.

### AZURE_AUTH_FILE
Default: ''
Path to the Azure auth file - terraform.azure.auto.tfvars.json found in the openshift install dir on the orchestration host i.e scale-ci-deploy/scale-ci-azure/terraform.azure.auto.tfvars.json.

### DEPLOYMENTS_PER_NS_TEST_PREFIX
Default: `deployments_per_ns`  
Test to prefix the pbench results.

### DEPLOYMENTS_PER_NS_CLEANUP
Default: `true`  
Enables/disables cluster loader cleanup of this workload on completion.

### DEPLOYMENTS_PER_NS_BASENAME
Default: `deployments-per-ns`  
Basename used by cluster loader for the project(s) it creates.

### DEPLOYMENTS_PER_NS_COUNT
Default: `2000`  
Maximum number of projects that will be created by the deployments-per-ns workload.

### DEPLOYMENTS_PER_NS_POD_IMAGE
Default: `gcr.io/google_containers/pause-amd64:3.0`  
Pod image to use for pods that are deployments-per-ns pods.

## Smoke test variables

```
DEPLOYMENTS_PER_NS_TEST_PREFIX=dpns_smoke
DEPLOYMENTS_PER_NS_CLEANUP=true
DEPLOYMENTS_PER_NS_BASENAME=dpns
DEPLOYMENTS_PER_NS_COUNT=1
DEPLOYMENTS_PER_NS_POD_IMAGE="gcr.io/google_containers/pause-amd64:3.0"
```
