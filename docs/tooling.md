# Tooling

The tooling playbook is `workloads/tooling.yml` and will configure [pbench](https://github.com/distributed-system-analysis/pbench) tooling on master, infra, and two worker nodes. Pbench tooling will permit the collection of pbench tools over a test duration. The default tools are disk, iostat, mpstat, oc, perf and pidstat. More information regarding pbench can be found at [https://github.com/distributed-system-analysis/pbench](https://github.com/distributed-system-analysis/pbench).

Running from CLI:

```sh
$ cp workloads/inventory.example inventory
$ # Add orchestration host to inventory
$ # Edit vars in workloads/vars/tooling.yml or define Environment vars (See below)
$ time ansible-playbook -vv -i inventory workloads/tooling.yml
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

### PBENCH_IMAGE
Default: `quay.io/openshift-scale/scale-ci-workload`  
Container image that runs pbench tooling.

### KUBECONFIG_FILE
Default: `~/.kube/config`  
Location of kubeconfig on orchestration host.

### PBENCH_SSH_PRIVATE_KEY_FILE
Default: `~/.ssh/id_rsa`  
Location of ssh private key to authenticate to the pbench results server.

### PBENCH_SSH_PUBLIC_KEY_FILE
Default: `~/.ssh/id_rsa.pub`  
Location of the ssh public key to authenticate to the pbench results server.

### PBENCH_SERVER
Default: There is no public default.  
DNS address of the pbench results server.
