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

# NodeVertical workload specific parameters:
NODEVERTICAL_TEST_PREFIX
NODEVERTICAL_MAXPODS
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

# PodVertical workload specific parameters:
PODVERTICAL_TEST_PREFIX
PODVERTICAL_MAXPODS
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

# MasterVertical workload specific parameters:
MASTERVERTICAL_TEST_PREFIX
MASTERVERTICAL_PROJECTS
MASTERVERTICAL_BASENAME
MASTERVERTICAL_CLEANUP
```
