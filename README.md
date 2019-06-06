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
