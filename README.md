# workloads

Tool an OpenShift cluster and run OpenShift Performance and Scale Workloads.

## Documentation, Usage and Examples

[See docs directory](docs/)

## Usage

1. Git clone the repo
2. Configure inventory file
3. Configure workload variables
4. Run the workload playbook(s)

```
$ git clone https://github.com/openshift-scale/workloads.git
$ cd workloads
$ cp workloads/inventory.example inventory
$ # Edit inventory and add your expected orchestration host
$ # Edit workload variables Ex vi workloads/vars/tooling.yml or define env variables
$ ansible-playbook -v -i inventory workloads/$workload.yml # Where $workload is the intended workload (Ex tooling.yml)
```

You can configure workload variables either by editing each workload's specific vars file found in workloads/vars/$workload.yml where $workload is intended workload or define the associated environment variables for each workload. Each variable is documented in the [docs directory of this repo](docs/)

## Usage in Continuous Integration (CI)

For Jenkins setups, each workload has a Jenkins job implemented in [scale-ci-pipeline](https://github.com/openshift-scale/scale-ci-pipeline)

To implement in your own CI, use an SCM plugin/module to clone the code into a build step. Each workload variable in the docs should be implemented as a parameter for each job. The build step can utilize additional variables in order to perform additional work such as login via a username and password. An example build step for NodeVertical implemented in Jenkins would look like:

```sh
set -o pipefail
set -eux

# Disable logging
set +x
oc login -u ${CLUSTER_USER} -p ${CLUSTER_PASSWORD} ${CLUSTER_API_URL}
# Re-enable logging
set -x

echo "[orchestration]" > inventory
echo "${ORCHESTRATION_HOST}" >> inventory

time ansible-playbook -vv -i inventory workloads/nodevertical.yml

oc logs --timestamps -n scale-ci-tooling -f job/scale-ci-nodevertical
oc get job -n scale-ci-tooling scale-ci-nodevertical -o json | jq -e '.status.succeeded==1'
```

For Pass/Fail functionality, jobs will not have a succeeded status and thus have failed in CI due to the last statement in the above build job.
