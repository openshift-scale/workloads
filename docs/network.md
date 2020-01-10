# Network Tests Workload

The network tests playbook is `workloads/network.yml` and will test latency and throughput of the cluster network. The tests leverage pbench-uperf to run uperf client/server pairs.

Requirements:
* Labeling Nodes
* HostNetwork - Cloud firewall configuration to permit traffic for ports `NETWORK_TEST_UPERF_SSHD_PORT`, uperf server ports between 20010-20109 and potentially `net.ipv4.ip_local_port_range` ports**

** Typically `net.ipv4.ip_local_port_range` is set to `32768 60999` in which uperf will pick a few random ports to send flags over. Currently there is no method outside of sysctls to control those ports - [See pbench issue #1238](https://github.com/distributed-system-analysis/pbench/issues/1238)

Running from CLI:

```sh
$ cp workloads/inventory.example inventory
$ # Add orchestration host to inventory
$ # Edit vars in workloads/vars/network.yml or define Environment vars (See below)
$ time ansible-playbook -vv -i inventory workloads/network.yml
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

### NETWORK_TEST_UPERF_IMAGE
Default: `quay.io/openshift-scale/scale-ci-uperf`  
Pod image to use for pods that are pbench-uperf pods.

### NETWORK_TEST_UPERF_SSHD_PORT
Default: `20000`  
Port for pbench to communicate via ssh to server/client pods. Typically this port is not blocked by any firewalls when conducting Pod to Pod and Pod to Service uperf benchmarks. Pod to Pod with HostNetwork will require that this port be opened in the cloud environment firewall along with the uperf port range.

### NETWORK_TEST_UPERF_PORT_RANGE
Default: `20010 20109`  
The range of ports in which uperf will incrementally use. Typical port increment pattern is 20010 for 1st pair, 20020 for 2nd pair...

### NETWORK_TEST_PREFIX
Default: `network`  
Test to prefix the pbench results.

### NETWORK_TEST_PAIRS
Default: `1`  
The number of client and server pairs of uperf that will be created for benchmarks. Pod to Pod with HostNetwork should *only* use a single pair at this time. `NETWORK_TEST_PAIRS` can be a comma separated list (Examples: `1` or `1,2,4`).

### NETWORK_TEST_BASENAME
Default: `network`  
Basename used by cluster loader for the project(s) it creates.

### NETWORK_TEST_HOSTNETWORK
Default: `false`  
Enables/disables HostNetwork in podspec for Pod to Pod benchmarks. HostNetwork *requires* "AUDIT_WRITE" capability per scc. Pods with HostNetwork can be used to test network bandwidth and latency without the pod SDN.

### NETWORK_TEST_SERVICE
Default: `false`  
Enables/disables creating service IPs in front of each uperf client/server pair. When enabled, pbench-uperf uses the service IP instead of the pod IP. This provides a method to test bandwidth and latency when a service IP is used.

### NETWORK_TEST_TYPE
Default: `stream,rr`  
The test types for uperf to run. Currently there are three settings: `stream`, `rr`, and `stream,rr`. Stream is a bandwidth test, rr is a request/response test for latency.

### NETWORK_TEST_RUNTIME
Default: `30`  
The length of time (in seconds) for upbench-uperf to run uperf. A higher value will produce a higher quality measurement at the expense of length of test time.

### NETWORK_TEST_MESSAGE_SIZES
Default: `64,1024,16384`  
The message sizes to have pbench-uperf run. This can be a comma separated list (Examples: `64` or `64,1024`).

### NETWORK_TEST_PROTOCOL
Default: `tcp,udp`  
The protocol(s) to have pbench-uperf test. This can be `tcp` or `udp` or `tcp,udp`.

### NETWORK_TEST_INSTANCES
Default: `1`  
The number of uperf threads that pbench-uperf will run per client/server pair. This can be a comma separated list (Examples: `1` or `1,2,4`).

### NETWORK_TEST_SAMPLES
Default: `3`  
The number of times pbench-uperf will run a given test combination and then pick the "best" sample for actual results.

### NETWORK_TEST_CLEANUP
Default: `true`  
Enables/disables if the network test will cleanup the project(s) that are created. Setting to `false` is for debugging purposes.

## Notes for typical Network testing execution

The recommended way to run the Network test is actually three iterations of the network playbook with the following parameters for each run. The first run would test Pod to Pod, the 2nd - Pod to Pod with HostNetwork, and the 3rd - Pod to Service which is the most expected usage inside an OpenShift Cluster (Pod -> Svc IP -> Pod).

### Suggested Pod to Pod Test Configuration

```
NETWORK_TEST_UPERF_IMAGE="quay.io/openshift-scale/scale-ci-uperf"
NETWORK_TEST_UPERF_SSHD_PORT=20000
NETWORK_TEST_UPERF_PORT_RANGE="20010 20109"
NETWORK_TEST_PREFIX=network
NETWORK_TEST_PAIRS=1,2,4
NETWORK_TEST_BASENAME=network
NETWORK_TEST_HOSTNETWORK=false
NETWORK_TEST_SERVICE=false
NETWORK_TEST_TYPE=stream,rr
NETWORK_TEST_RUNTIME=60
NETWORK_TEST_MESSAGE_SIZES=64,1024,16384
NETWORK_TEST_PROTOCOL=tcp,udp
NETWORK_TEST_INSTANCES=1,2
NETWORK_TEST_SAMPLES=3
NETWORK_TEST_CLEANUP=true
```

### Suggested Pod to Pod HostNetwork Test Configuration

```
NETWORK_TEST_UPERF_IMAGE="quay.io/openshift-scale/scale-ci-uperf"
NETWORK_TEST_UPERF_SSHD_PORT=20000
NETWORK_TEST_UPERF_PORT_RANGE=""
NETWORK_TEST_PREFIX=network
NETWORK_TEST_PAIRS=1
NETWORK_TEST_BASENAME=network
NETWORK_TEST_HOSTNETWORK=true
NETWORK_TEST_SERVICE=false
NETWORK_TEST_TYPE=stream,rr
NETWORK_TEST_RUNTIME=60
NETWORK_TEST_MESSAGE_SIZES=64,1024,16384
NETWORK_TEST_PROTOCOL=tcp,udp
NETWORK_TEST_INSTANCES=1,2,4
NETWORK_TEST_SAMPLES=3
NETWORK_TEST_CLEANUP=true
```

Make sure `NETWORK_TEST_UPERF_SSHD_PORT` is enabled in your cloud environment to permit traffic when using HostNetwork. HostNetwork *requires* "AUDIT_WRITE" capability per scc.

### Suggested Pod to Service Test Configuration

```
NETWORK_TEST_UPERF_IMAGE="quay.io/openshift-scale/scale-ci-uperf"
NETWORK_TEST_UPERF_SSHD_PORT=20000
NETWORK_TEST_UPERF_PORT_RANGE="20010 20109"
NETWORK_TEST_PREFIX=network
NETWORK_TEST_PAIRS=1,2,4
NETWORK_TEST_BASENAME=network
NETWORK_TEST_HOSTNETWORK=false
NETWORK_TEST_SERVICE=true
NETWORK_TEST_TYPE=stream,rr
NETWORK_TEST_RUNTIME=60
NETWORK_TEST_MESSAGE_SIZES=64,1024,16384
NETWORK_TEST_PROTOCOL=tcp,udp
NETWORK_TEST_INSTANCES=1,2
NETWORK_TEST_SAMPLES=3
NETWORK_TEST_CLEANUP=true
```

## Smoke tests variables

Smoke tests are meant to be a quick check to see if the network benchmark is operational before running lengthy full run with many iterations and combinations. It is suggested to have the pbench-agents disabled for the smoke tests as well to speed up this process.

### Pod to Pod smoke test

```
NETWORK_TEST_UPERF_IMAGE="quay.io/openshift-scale/scale-ci-uperf"
NETWORK_TEST_UPERF_SSHD_PORT=20000
NETWORK_TEST_UPERF_PORT_RANGE="20010 20109"
NETWORK_TEST_PREFIX=network
NETWORK_TEST_PAIRS=1
NETWORK_TEST_BASENAME=network
NETWORK_TEST_HOSTNETWORK=false
NETWORK_TEST_SERVICE=false
NETWORK_TEST_TYPE=stream
NETWORK_TEST_RUNTIME=30
NETWORK_TEST_MESSAGE_SIZES=1024
NETWORK_TEST_PROTOCOL=tcp
NETWORK_TEST_INSTANCES=1
NETWORK_TEST_SAMPLES=1
NETWORK_TEST_CLEANUP=true
```

### Pod to Pod HostNetwork smoke test

```
NETWORK_TEST_UPERF_IMAGE="quay.io/openshift-scale/scale-ci-uperf"
NETWORK_TEST_UPERF_SSHD_PORT=20000
NETWORK_TEST_UPERF_PORT_RANGE=""
NETWORK_TEST_PREFIX=network
NETWORK_TEST_PAIRS=1
NETWORK_TEST_BASENAME=network
NETWORK_TEST_HOSTNETWORK=true
NETWORK_TEST_SERVICE=false
NETWORK_TEST_TYPE=stream
NETWORK_TEST_RUNTIME=30
NETWORK_TEST_MESSAGE_SIZES=1024
NETWORK_TEST_PROTOCOL=tcp
NETWORK_TEST_INSTANCES=1
NETWORK_TEST_SAMPLES=1
NETWORK_TEST_CLEANUP=true
```

### Pod to Service smoke test

```
NETWORK_TEST_UPERF_IMAGE="quay.io/openshift-scale/scale-ci-uperf"
NETWORK_TEST_UPERF_SSHD_PORT=20000
NETWORK_TEST_UPERF_PORT_RANGE="20010 20109"
NETWORK_TEST_PREFIX=network
NETWORK_TEST_PAIRS=1
NETWORK_TEST_BASENAME=network
NETWORK_TEST_HOSTNETWORK=false
NETWORK_TEST_SERVICE=true
NETWORK_TEST_TYPE=stream
NETWORK_TEST_RUNTIME=30
NETWORK_TEST_MESSAGE_SIZES=1024
NETWORK_TEST_PROTOCOL=tcp
NETWORK_TEST_INSTANCES=1
NETWORK_TEST_SAMPLES=1
NETWORK_TEST_CLEANUP=true
```
