# Services per namespace Workload

The services per namespace workload playbook is `workloads/cluster-limits-services-per-namespace.yml` and will run the services per namespace workload on your cluster.

Services per namespace is a cluster limits focused test which creates maximum possible services per each namespace.

Running from CLI:

```sh
$ cp workloads/inventory.example inventory
$ # Add orchestration host to inventory
$ # Edit vars in workloads/vars/services-per-namespace.yml or define Environment vars (See below)
$ time ansible-playbook -vv -i inventory workloads/cluster-limits-services-per-namespace.yml
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

### SERVICES_PER_NAMESPACE_TEST_PREFIX
Default: `services_per_namespace`  
Test to prefix the pbench results.

### SERVICES_PER_NAMESPACE_CLEANUP
Default: `true`  
Enables/disables cluster loader cleanup of this workload on completion.

### SERVICES_PER_NAMESPACE_BASENAME
Default: `services-per-namespace`  
Basename used by cluster loader for the project(s) it creates.

### SERVICES_PER_NAMESPACE_PROJECTS
Default: `2`  
Maximum number of projects that will be created by the services per namespace workload. Typically much higher values are used than the default for large scale tests.

### SERVICES_PER_NAMESPACE_COUNT
Default: `5000`
Maximum number of services per namespace that will be created by the services per namespace workload.

### EXPECTED_SERVICES_PER_NAMESPACE_DURATION
Default: `600`  
Pass/fail criteria. Value to determine if Namespaces per cluster workload executed in duration expected.

## Smoke test variables

```
SERVICES_PER_NAMESPACE_TEST_PREFIX=services_per_namespace_smoke
SERVICES_PER_NAMESPACE_CLEANUP=true
SERVICES_PER_NAMESPACE_BASENAME=services-per-namespace
SERVICES_PER_NAMESPACE_PROJECTS=2
SERVICES_PER_NAMESPACE_COUNT=100
```
