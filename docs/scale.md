# Scale Workload

The scale workload playbook is `workloads/scale.yml` and will scale a cluster with or without tooling.

The scale workload can scale a cluster both with more or less worker nodes provisioned across 1-4 availability zones.  If scaling down it is best to use the workload node to host the workload job as the nodes chosen to host the workload Pod could also be a node that is removed.

Running from CLI:

```sh
$ cp workloads/inventory.example inventory
$ # Add orchestration host to inventory
$ # Edit vars in workloads/vars/scale.yml or define Environment vars (See below)
$ time ansible-playbook -vv -i inventory workloads/scale.yml
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
Default: `false`  
Enables/disables running the workload Pod as privileged.

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

### SCALE_TEST_PREFIX
Default: `scale`  
Test to prefix the pbench results.

### SCALE_METADATA_PREFIX
Default: `machine.openshift.io`  
Depending on the release/build the openshift-machine-api may use a different prefix for the metadata label used to determine the cluster name. This is either `machine.openshift.io` or `sigs.k8s.io`.

### SCALE_WORKER_COUNT
Default: `5`  
Number of worker nodes to scale to. They will be spread as evenly as possible across 4 availability zones.

### SCALE_POLL_ATTEMPTS
Default: `600`  
Number of times to poll to determine if "Ready" worker count has scaled.  Each poll has a 2 second sleep between polling attempts.

### EXPECTED_SCALE_DURATION
Default: `600`  
Pass/fail criteria. Value to determine if scale workload executed in duration expected.

## Smoke test variables

```
SCALE_TEST_PREFIX=scale_smoke
SCALE_METADATA_PREFIX=machine.openshift.io
SCALE_WORKER_COUNT=5
SCALE_POLL_ATTEMPTS=600
EXPECTED_SCALE_DURATION=600
```
