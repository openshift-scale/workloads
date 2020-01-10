# PVCScale Workload

The PVCScale workload playbook is `workloads/pvcscale.yml` and will run the pvcscale workload on your cluster.

PVCScale workload stresses storage backend and determines how fast Pods are started depending on storage backend. Each Pod requires a PVC to be allocated and attached.

PVCScale test creates a desired number of Pods and attaches one PVC to each Pod, it supports only dynamic PVC allocation using storage class and in order for this test to work it is a requirement to have a working storage class which can be used to allocate a PVC.

At end of test, the time necessary for Pods to reach `READY` state with PVC in `Bound` status is measured, and based on this time we can compare start time for same number of Pods/PVCS for cases when only difference is storage class used to allocate PVCS.

When the Pod is in `READY` state and the PVC in `Bound` status it is considered that the PVC is ready for consumption by application Pod.

Different storage backends behaves differently when it comes to PVC scalability and it is necessary to have repeatable results which can answer the question what storage backend provides optimal PVCs scale and density results for scenario where many Pods/PVCs are necessary to start in short period of time.

Running from CLI:

```sh
$ cp workloads/inventory.example inventory
$ # Add orchestration host to inventory
$ # Edit vars in workloads/vars/pvcscale.yml or define Environment vars (See below)
$ time ansible-playbook -vv -i inventory workloads/pvcscale.yml
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
Enables/disables running the workload Pod as privileged.

### KUBECONFIG_FILE
Default: `~/.kube/config`  
Location of kubeconfig on orchestration host. PVCScale tests supports only default location for `kubeconfig` file.

### PBENCH_SSH_PRIVATE_KEY_FILE
Default: `~/.ssh/id_rsa`  
Location of ssh private key to authenticate to the pbench results server.

### PBENCH_SSH_PUBLIC_KEY_FILE
Default: `~/.ssh/id_rsa.pub`  
Location of the ssh public key to authenticate to the pbench results server.

### ENABLE_PBENCH_AGENTS
Default: `false`  
Enables/disables the collection of pbench data on the pbench agent Pods. These Pods are deployed by the workloads/tooling.yml playbook it needs to be executed independently of PVCScale test.

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

### PVCSCALE_TEST_PREFIX
Default: `pvcscale`  
Test to prefix the pbench results.

### PVCSCALE_CLEANUP
Default: `true`  
Enables/disables cleanup of this workload on completion. Cleanup in PVCScale terminology means deletion of `pvcscale0` project. This operation can be resource intensive in case there are many Pods/PVCs  in `pvcscale0` project which are created during the test.

### PVCSCALE_BASENAME
Default: `pvcscale`  
Basename used by cluster loader for the project(s) it creates.

### PVCSCALE_MAXPODS
Default: `10`  
Maximum number of Pods that will be created by the pvcscale workload. Typically much higher values are used than the default for large scale tests. PVCScale does not implement a mechanism to calculate maximum number of Pods which can be started on a cluster, so it is necessary to adjust `PVCSCALE_MAXPODS` to cluster capacity and execute it accordingly. Choosing `PVCSCALE_MAXPODS` to be high can lead that many Pods/PVCs being created which will affect cluster limits and peformance.

### PVCSCALE_POD_IMAGE
Default: `quay.io/openshift-scale/scale-ci-workload`  
Pod container image to use for Pods that are pvscale Pods. This image will be used to start Pod and bound PVC to it.

### PVCSCALE_STEPSIZE
Default: `1`  
Number of Pods for cluster loader will create before waiting for Pods to become running. PVCScale test creates one Pod/PVC at time.

### PVCSCALE_PAUSE
Default: `0`  
Period of time (in seconds) for cluster loader to pause after creating Pods and waiting for them to be running. PVCScale does not wait on Pods to be `READY` state before it creates new ones.

### PVCSCALE_STORAGE_SIZE
Default: `1Gi`  
This is default PVC size. If specified as `1Gi` then all Pods will get `1Gi` PVC mounted to `/mnt/pvcmount` inside Pod.

### PVCSCALE_STORAGECLASS
Default: None  
The storageclass which will be used to allocated PVCs. It has to be present on cluster and must be functional for test to work. Default is unset and must be changed prior running test and adapted to name of functional storage class name.

### PVCSCALE_ACCESS_MODES
Default: `ReadWriteOnce`  
`PVCSCALE_ACCESS_MODES` is access_mode for PVC inside Pod. This field does not affect the test itself and might be removed in the future.

### PVCSCALE_NODESELECTOR

Default: None
If there is need for Pods to start on specific nodes based on node selector this parameter will ensure that Pods
are starting on nodes with desired labels. It is prerequst that nodes are labeled with proper labels.
### Smoke test variables

```
ORCHESTRATION_HOST="localhost"
ORCHESTRATION_USER=root
WORKLOAD_JOB_PRIVILEGED=false
KUBECONFIG_FILE='~/.kube/config'
# adapt PUBLIC_KEY and PRIVATE_KEY
PBENCH_SSH_PRIVATE_KEY_FILE='~/.ssh/id_rsa'
PBENCH_SSH_PUBLIC_KEY_FILE='~/.ssh/id_rsa.pub'
ENABLE_PBENCH_AGENTS=false
PBENCH_SERVER=pbench.dev.openshift.com
PVCSCALE_TEST_PREFIX=pvcscale
PVCSCALE_CLEANUP=false
PVCSCALE_BASENAME=pvcscale
PVCSCALE_MAXPODS=10
PVCSCALE_POD_IMAGE="quay.io/openshift-scale/scale-ci-workload"
PVCSCALE_STEPSIZE=1
PVCSCALE_PAUSE=0
PVCSCALE_STORAGE_SIZE="1Gi"
PVCSCALE_STORAGECLASS="storgage_class_name"
ACCESS_MODES="ReadWriteOnce"
# adapt PUBLIC_KEY and PRIVATE_KEY to your needs
PUBLIC_KEY=~/.ssh/id_rsa.pub
PRIVATE_KEY=~/.ssh/id_rsa
echo "[orchestration]" > inventory
echo "${ORCHESTRATION_HOST}" >> inventory
```

After exporting above variables, execute test:

```
time ansible-playbook -v -i inventory workloads/workloads/pvcscale.yml
```

### Test results

PVCScale test creates first the number of Pods specified in `PVCSCALE_MAXPODS` variable. Once all Pods are in `READY` state with PVC in `Bound` status,  PVCScale test collects data for all Pods when they are started, and from this information we can then see how long it takes to start Pods from Pod1..PodN.

The output will resemble:

```
Pod-Ready
07:56:29
07:56:30
07:56:31
07:56:32
07:56:35
07:56:36
07:56:39
07:56:39
07:56:44
07:56:46
```

In this example the visible times are when Pods were in `READY` state. The first Pod is started at `07:56:29` and last Pod at `07:56:46`. The time in this output is day time when test was executed, and this will be changed in future versions of this test to be independent of time when test was ran. In this case the difference between last pod start time and first pod start time is total time necessary for Pods to be in `READY` state with PVC in `Bound` status.
