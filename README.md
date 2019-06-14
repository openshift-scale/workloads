# workloads

Tool a RHCOS OpenShift cluster and run RHCOS OpenShift Performance and Scale Workloads

## RHCOS Tooling

The RHCOS tooling playbook is `workloads/tooling.yml` and will configure pbench tooling on master, infra, and two worker nodes.

Running from CLI:

```sh
$ cp workloads/inventory.example inventory
$ # Add orchestration host to inventory
$ # Edit vars in workloads/vars/tooling.yml or define Environment vars (See below)
$ time ansible-playbook -vv -i inventory workloads/tooling.yml
```

### Environment variables for RHCOS tooling playbook

```
###############################################################################
# Ansible SSH variables.
###############################################################################
PUBLIC_KEY
PRIVATE_KEY

ORCHESTRATION_USER
###############################################################################
# RHCOS tooling variables.
###############################################################################
PBENCH_IMAGE

KUBECONFIG_FILE

PBENCH_SSH_PRIVATE_KEY_FILE
PBENCH_SSH_PUBLIC_KEY_FILE
PBENCH_SERVER
```

## RHCOS Test Workload

The RHCOS test workload playbook is `workloads/test.yml` and will run a ssh-able container on your RHCOS cluster.  This container can be used to provide proof of concept and or ad-hoc testing with the tooling in place in order to develop new workloads.

Running from CLI:

```sh
$ cp workloads/inventory.example inventory
$ # Add orchestration host to inventory
$ # Edit vars in workloads/vars/test.yml or define Environment vars (See below)
$ time ansible-playbook -vv -i inventory workloads/test.yml
```

### Environment variables for RHCOS test playbook

```
###############################################################################
# Ansible SSH variables.
###############################################################################
PUBLIC_KEY
PRIVATE_KEY

ORCHESTRATION_USER
###############################################################################
# RHCOS test workload variables.
###############################################################################
WORKLOAD_IMAGE

KUBECONFIG_FILE

PBENCH_SSH_PRIVATE_KEY_FILE
PBENCH_SSH_PUBLIC_KEY_FILE
PBENCH_SERVER
```

## RHCOS Baseline Workload

The RHCOS baseline workload playbook is `workloads/baseline.yml` and will run the baseline workload on your RHCOS cluster.

Running from CLI:

```sh
$ cp workloads/inventory.example inventory
$ # Add orchestration host to inventory
$ # Edit vars in workloads/vars/baseline.yml or define Environment vars (See below)
$ time ansible-playbook -vv -i inventory workloads/baseline.yml
```

### Environment variables for RHCOS baseline workload playbook

```
###############################################################################
# Ansible SSH variables.
###############################################################################
PUBLIC_KEY
PRIVATE_KEY

ORCHESTRATION_USER
###############################################################################
# RHCOS workload variables.
###############################################################################
WORKLOAD_IMAGE

WORKLOAD_JOB_NODE_SELECTOR
WORKLOAD_JOB_TAINT
WORKLOAD_JOB_PRIVILEGED

KUBECONFIG_FILE

PBENCH_SSH_PRIVATE_KEY_FILE
PBENCH_SSH_PUBLIC_KEY_FILE
ENABLE_PBENCH_AGENTS
PBENCH_SERVER

SCALE_CI_RESULTS_TOKEN
JOB_COMPLETION_POLL_ATTEMPTS

# Baseline workload specific parameters:
BASELINE_TEST_PREFIX
BASELINE_WORKLOAD_DURATION
```

## RHCOS NodeVertical Workload

The RHCOS nodevertical workload playbook is `workloads/nodevertical.yml` and will run the nodevertical workload on your RHCOS cluster.

Requirements:
* labeling nodes

Running from CLI:

```sh
$ cp workloads/inventory.example inventory
$ # Add orchestration host to inventory
$ # Edit vars in workloads/vars/nodevertical.yml or define Environment vars (See below)
$ time ansible-playbook -vv -i inventory workloads/nodevertical.yml
```

### Environment variables for RHCOS nodevertical workload playbook

```
###############################################################################
# Ansible SSH variables.
###############################################################################
PUBLIC_KEY
PRIVATE_KEY

ORCHESTRATION_USER
###############################################################################
# RHCOS workload variables.
###############################################################################
WORKLOAD_IMAGE

WORKLOAD_JOB_NODE_SELECTOR
WORKLOAD_JOB_TAINT
WORKLOAD_JOB_PRIVILEGED

KUBECONFIG_FILE

PBENCH_SSH_PRIVATE_KEY_FILE
PBENCH_SSH_PUBLIC_KEY_FILE
ENABLE_PBENCH_AGENTS
PBENCH_SERVER

SCALE_CI_RESULTS_TOKEN
JOB_COMPLETION_POLL_ATTEMPTS

# NodeVertical workload specific parameters:
NODEVERTICAL_TEST_PREFIX
NODEVERTICAL_CLEANUP
NODEVERTICAL_BASENAME
NODEVERTICAL_MAXPODS
NODEVERTICAL_POD_IMAGE
NODEVERTICAL_STEPSIZE
NODEVERTICAL_PAUSE
```

## RHCOS PodVertical Workload

The RHCOS podvertical workload playbook is `workloads/podvertical.yml` and will run the podvertical workload on your RHCOS cluster.

Running from CLI:

```sh
$ cp workloads/inventory.example inventory
$ # Add orchestration host to inventory
$ # Edit vars in workloads/vars/podvertical.yml or define Environment vars (See below)
$ time ansible-playbook -vv -i inventory workloads/podvertical.yml
```

### Environment variables for RHCOS podvertical workload playbook

```
###############################################################################
# Ansible SSH variables.
###############################################################################
PUBLIC_KEY
PRIVATE_KEY

ORCHESTRATION_USER
###############################################################################
# RHCOS workload variables.
###############################################################################
WORKLOAD_IMAGE

WORKLOAD_JOB_NODE_SELECTOR
WORKLOAD_JOB_TAINT
WORKLOAD_JOB_PRIVILEGED

KUBECONFIG_FILE

PBENCH_SSH_PRIVATE_KEY_FILE
PBENCH_SSH_PUBLIC_KEY_FILE
ENABLE_PBENCH_AGENTS
PBENCH_SERVER

SCALE_CI_RESULTS_TOKEN
JOB_COMPLETION_POLL_ATTEMPTS

# PodVertical workload specific parameters:
PODVERTICAL_TEST_PREFIX
PODVERTICAL_CLEANUP
PODVERTICAL_BASENAME
PODVERTICAL_MAXPODS
PODVERTICAL_POD_IMAGE
PODVERTICAL_STEPSIZE
PODVERTICAL_PAUSE
```

## RHCOS MasterVertical Workload

The RHCOS mastervertical workload playbook is `workloads/mastervertical.yml` and will run the mastervertical workload on your RHCOS cluster.

Running from CLI:

```sh
$ cp workloads/inventory.example inventory
$ # Add orchestration host to inventory
$ # Edit vars in workloads/vars/mastervertical.yml or define Environment vars (See below)
$ time ansible-playbook -vv -i inventory workloads/mastervertical.yml
```

### Environment variables for RHCOS mastervertical workload playbook

```
###############################################################################
# Ansible SSH variables.
###############################################################################
PUBLIC_KEY
PRIVATE_KEY

ORCHESTRATION_USER
###############################################################################
# RHCOS workload variables.
###############################################################################
WORKLOAD_IMAGE

WORKLOAD_JOB_NODE_SELECTOR
WORKLOAD_JOB_TAINT
WORKLOAD_JOB_PRIVILEGED

KUBECONFIG_FILE

PBENCH_SSH_PRIVATE_KEY_FILE
PBENCH_SSH_PUBLIC_KEY_FILE
ENABLE_PBENCH_AGENTS
PBENCH_SERVER

SCALE_CI_RESULTS_TOKEN
JOB_COMPLETION_POLL_ATTEMPTS

# MasterVertical workload specific parameters:
MASTERVERTICAL_TEST_PREFIX
MASTERVERTICAL_CLEANUP
MASTERVERTICAL_BASENAME
MASTERVERTICAL_PROJECTS
```

## RHCOS HTTP Workload

The RHCOS HTTP workload playbook is `workloads/http.yml` and will run the HTTP(s) workload on your RHCOS cluster.

Running from CLI:

```sh
$ cp workloads/inventory.example inventory
$ # Add orchestration host to inventory
$ # Edit vars in workloads/vars/http.yml or define Environment vars (See below)
$ time ansible-playbook -vv -i inventory workloads/http.yml
```

### Environment variables for RHCOS HTTP workload playbook

```
###############################################################################
# Ansible SSH variables.
###############################################################################
PUBLIC_KEY
PRIVATE_KEY

ORCHESTRATION_USER
###############################################################################
# RHCOS workload variables.
###############################################################################
WORKLOAD_IMAGE

KUBECONFIG_FILE

PBENCH_SSH_PRIVATE_KEY_FILE
PBENCH_SSH_PUBLIC_KEY_FILE
ENABLE_PBENCH_AGENTS
PBENCH_SERVER

SCALE_CI_RESULTS_TOKEN
JOB_COMPLETION_POLL_ATTEMPTS

# HTTP workload specific parameters:
TEST_CFG
PBENCH_USE
PBENCH_SCRAPER_USE
CLEAR_RESULTS
MOVE_RESULTS
SERVER_RESULTS
SERVER_RESULTS_SSH_KEY
LOAD_GENERATORS
LOAD_GENERATOR_NODES
CL_PROJECTS
CL_TEMPLATES
RUN_TIME
MB_DELAY
MB_TLS_SESSION_REUSE
MB_METHOD
MB_RESPONSE_SIZE
MB_REQUEST_BODY_SIZE
ROUTE_TERMINATION
SMOKE_TEST
NAMESPACE_CLEANUP
HTTP_STRESS_CONTAINER_IMAGE
HTTP_SERVER_CONTAINER_IMAGE
```

## RHCOS Network tests Workload

The RHCOS network tests workload playbook is `workloads/network.yml` and is used to test latency and throughput of the cluster network.

Requirements:
* All Network tests - labeling nodes
* HostNetwork - privileged pods
* HostNetwork - Cloud firewall configuration to permit traffic for ports between 20010-20100 and NETWORK_TEST_UPERF_SSHD_PORT

Running from CLI:

```sh
$ cp workloads/inventory.example inventory
$ # Add orchestration host to inventory
$ # Edit vars in workloads/vars/network.yml or define Environment vars (See below)
$ time ansible-playbook -vv -i inventory workloads/network.yml
```

### Environment variables for RHCOS network workload playbook

```
###############################################################################
# Ansible SSH variables.
###############################################################################
PUBLIC_KEY
PRIVATE_KEY

ORCHESTRATION_USER
###############################################################################
# RHCOS workload variables.
###############################################################################
WORKLOAD_IMAGE

WORKLOAD_JOB_NODE_SELECTOR
WORKLOAD_JOB_TAINT
WORKLOAD_JOB_PRIVILEGED

KUBECONFIG_FILE

PBENCH_SSH_PRIVATE_KEY_FILE
PBENCH_SSH_PUBLIC_KEY_FILE
ENABLE_PBENCH_AGENTS
PBENCH_SERVER

SCALE_CI_RESULTS_TOKEN
JOB_COMPLETION_POLL_ATTEMPTS

# Network benchmarks specific parameters:
NETWORK_TEST_UPERF_IMAGE
NETWORK_TEST_UPERF_SSHD_PORT
NETWORK_TEST_UPERF_PORT_RANGE
NETWORK_TEST_PREFIX
NETWORK_TEST_PAIRS
NETWORK_TEST_BASENAME
NETWORK_TEST_HOSTNETWORK
NETWORK_TEST_SERVICE
NETWORK_TEST_TYPE
NETWORK_TEST_RUNTIME
NETWORK_TEST_MESSAGE_SIZES
NETWORK_TEST_PROTOCOL
NETWORK_TEST_INSTANCES
NETWORK_TEST_SAMPLES
NETWORK_TEST_CLEANUP
```

### Notes for Network testing execution

#### Suggested Pod2Pod Test Configuration

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

#### Suggested Pod2Pod HostNetwork Test Configuration

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

Make sure NETWORK_TEST_UPERF_SSHD_PORT is enabled in your cloud environment to permit traffic when using HostNetwork.  HostNetwork testing requires privileged pods.

#### Suggested Service 2 Service Test Configuration

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
