# FIO I/O  Workload

The FIO I/O workload playbook is `workloads/fio.yml` and will run the FIO I/O workload on your cluster.
FIO I/O workload test is designed to stress storage backend used by
application pods. FIO I/O workload test supports all FIO test types : seq read /
write, random read/write, randrw, rw.


Requirements:

* Working storage backend and working storage class

FIO I/O test will use dynamic provisioning to allocate PVC and attach it to Pod
so it requirement to have working storage class prior starting this test.

* `PBENCH_SERVER` variable to set to valid pbench server

How to setup pbench server is described in pbench
[documentation](https://distributed-system-analysis.github.io/pbench/doc/server/installation.html)


Running from CLI:

```sh
$ cp workloads/inventory.example inventory
$ # Add orchestration host to inventory
$ # Edit vars in workloads/vars/fio.yml or define Environment vars (See below)
$ time ansible-playbook -vv -i inventory workloads/fio.yml
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

### ENABLE_PBENCH_AGENTS

Default: `false`  
Enables/disables the collection of pbench data on the pbench agent Pods. These Pods are deployed by the tooling playbook
and if this option is enabled then tooling playbook needs to be executed prior to this test.

### ENABLE_PBENCH_COPY

Default: `true`  

Enables/disables the copying of pbench data to a remote results server for further analysis.
As of now, this test requires valid `PBENCH_SERVER` where it will copy results at end of test.

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

Default: `10000`  

Number of retries for Ansible to poll if the workload job has completed.
Poll attempts delay 10s between polls with some additional time taken for each polling action depending on the orchestration host setup.
FIO I/O test for many pods and big file sizes can run for hours and either we rise `JOB_COMPLETION_POLL_ATTEMPTS` to
higt value, or remove fully checking for `JOB_COMPLETION_POLL_ATTEMPTS` for FIO I/O test.


### AZURE_AUTH
Default: false
Set it to true when running OCP on Azure.

### AZURE_AUTH_FILE
Default: ''
Path to the Azure auth file - terraform.azure.auto.tfvars.json found in the openshift install dir on the orchestration host i.e scale-ci-deploy/scale-ci-azure/terraform.azure.auto.tfvars.json.

### FIOTEST_PREFIX

Default: `fiotest`

Prefix to use for FIO I/O test

### FIOTEST_CLEANUP

Default: `true`
If set to `true` test project will be removed at end of test.

### FIOTEST_BASENAME

Default: `fiotest`  
Basename used by cluster loader for the project(s) it creates.

### FIOTEST_MAXPODS

Default: `1`  
Maximum number of Pods that FIO I/O test will create for test

### FIOTEST_POD_IMAGE

Default: `quay.io/openshift-scale/scale-ci-fio:latest`

Container image to use for FIO Pods

### FIOTEST_STEPSIZE

Default: `1`  
Number of Pods for cluster loader will create before waiting for Pods to become running.

### FIOTEST_PAUSE

Default: `0`  
Period of time (in seconds) for cluster loader to pause after creating Pods and waiting for them to be "Running" state.
When `FIOTEST_PAUSE` is zero, cluster loader will create pods in fastest possible manner.

### FIOTEST_STORAGE_SIZE

Default: `2Gi`

`FIOTEST_STORAGE_SIZE` defines size of PVC which will be created and mounted to Pod. It is important to notice that this
cannot be smaller than `FIOTEST_FILESIZE`

### FIOTEST_STORAGECLASS

Default: ``

This parameter defines what storageclass to use to dynamically allocate PVC. It is expected that storage class is
present and functional in order for test to work.
Storage class name will be different and depends on environment, common storage class names are

* AWS - gp2
* Azure - managed-premium
* OSP - standard
* CEPH - csi-cephfs/csi-rbd


### FIOTEST_ACCESS_MODES

Default: `ReadWriteOnce`

`FIOTEST_ACCESS_MODES` is responsible for PVC access mode. This parameter will accept one of `ReadWriteOnce` ,
`ReadWriteMany` or `ReadOnlyMany`. It is important to understand that particular access mode must be supported
by storage used for test.

### FIOTEST_BS

Default: `4`

Fio block size.

### FIOTEST_FILENAME

Default: `/mnt/pvcmount/f2`

FIO file to write. PVC is mounted inside FIO pod to `/mnt/pvcmount` and thus inside this mount point fio file is
created. This ensures that I/O operations are executed against PVC.

### FIOTEST_FILESIZE

Default: `1GB`

FIO file size and its size cannot exceed `FIOTEST_STORAGE_SIZE`.

### FIOTEST_RUNTIME

Default: `60`

FIO test runtime

### FIOTEST_DIRECT

Default: `1`

From `man fio` - If  value  is  true,  use  non-buffered  I/O.  
This is usually O_DIRECT.

### FIOTEST_IODEPTH

Default: `1`

Number of I/O units to keep in flight  against  the  file

### FIOTEST_TESTTYPE

Default: `read`

FIO test type to execute. Default is `read`, supported are : read,write,randread,randwrite,randrw,rw

### FIOTEST_SAMPLES

Default: `1`

Running one iteration of test can give misleading results, and it is recommended to run multiple iterations to catch up
deviations and anomalies. Test result will show best iteration.

### FIOTEST_NODESELECTOR

Default: ""

For cases when it is necessary to have FIO pods to be assigned to already labeled nodes with specific label
`FIOTEST_NODESELECTOR` allows to specify desired label.
FIO I/O test does not label nodes, it expect that labels are already assigned to nodes.



### Smoke test variables

```
FIOTEST_PREFIX=fiotest
FIOTEST_CLEANUP=true
FIOTEST_BASENAME=fiotest
ENABLE_PBENCH_COPY=true
FIOTEST_MAXPODS=1
FIOTEST_POD_IMAGE="quay.io/openshift-scale/scale-ci-fio"
FIOTEST_STEPSIZE=1
FIOTEST_PAUSE=0
FIOTEST_STORAGE_SIZE="2Gi"
FIOTEST_STORAGECLASS=gp2
FIOTEST_ACCESS_MODES="ReadWriteOnce"
FIOTEST_BS=4
FIOTEST_FILENAME="/mnt/pvcmount/f2"
FIOTEST_FILESIZE="1GB"
FIOTEST_RUNTIME=600
FIOTEST_DIRECT=1
FIOTEST_IODEPTH=1
FIOTEST_TESTTYPE=write
FIOTEST_SAMPLES=1

```

### Additional observations
For combination with many test pods and FIO file size in range of GBs - FIO I/O test can take long to finish and it can
happen that `JOB_COMPLETION_POLL_ATTEMPTS` expires before test is done. `JOB_COMPLETION_POLL_ATTEMPTS` is increased to
10000 what does not affect test duration. It is necessary to to decide is `JOB_COMPLETION_POLL_ATTEMPTS` relevant for
FIO I/O test and can it be fully ignored.
