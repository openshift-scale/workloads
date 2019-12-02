# Workload Contributing Guidelines

## Overview

Each workload in the openshift-scale/workloads repo attempts to as closely as possible adhere to a set of guidelines regarding structure and format.

The guidelines help reduce the overall debugging time to get a workload up and running. With each workload following the same set of guidelines, it makes debugging and fixing a problem much easier as each workload does not require you to learn an entirely different method and/or structure for doing something.

For example, each workload slurps the kubeconfig and provides it as a kubernetes secret to the scale-ci-workload pod, thus there is only one way that the kubeconfig is obtained and provided. You only have to know one method to do so and where the lines of code are for it in-case there is an issue with the kubeconfig.

Guidelines:
* Each workload should map to a single playbook
* All files for a workload (playbook, vars, docs file) should strive to use the same name
* One single vars file per workload playbook
* All externally-provided variables to a workload should be defined in the workload vars file
* Variable plumbing for workloads and [scale-ci-deploy](https://github.com/openshift-scale/scale-ci-deploy) playbooks should "mimic" each other
* Externally-provided variables are plumbed in via environment variables
* Environment variables are always UPPER case, keep Ansible vars lower case
* Binaries and executable code should be built into [scale-ci-workload](https://github.com/openshift-scale/images/blob/master/scale-ci-workload/Dockerfile) image
* Nothing should be installed while the workload is running (Unless that is the workload for whatever reason)
* Workloads should be as deterministic as possible (Nothing should change itself in a non-reproducible/non-predictable way)
* Each workload needs a docs file that describes its variables
* Fail as early as possible
* Workload scripts if possible should strive to have the least number of templating sources (Example: consider not having the workload script be a jinja2 template as a workload script can template bash environment variables and OpenShift templates already. The less templating means less confusion over the source of a variable)
* Workloads should avoid `git clone` on another repo as a best practice

## What does a typical workload consist of:

A workload consists of

* Workload playbook
* Workload vars file
* Workload configmap script
* Workload environment vars
* Workload job pod
* Binaries/executables provided in the container image
* Workload docs file

Example (Network workload)
* [Network playbook](../workloads/network.yml)
* [Network vars](../workloads/vars/network.yml)
* [Network configmap script](../workloads/templates/workload-network-script-cm.yml.j2)
* [Network environment vars](../workloads/templates/workload-env.yml.j2)
* [Workload job resource](../workloads/templates/workload-job.yml.j2)
* [Network uperf image Dockerfile](https://github.com/openshift-scale/images/blob/master/scale-ci-uperf/Dockerfile)
* [Network workload docs](network.md)

## Creating your own workload

The recommended method to creating or adding your own workload is to use the NodeVertical workload as a "Template" to follow.

### Suggested steps to add a workload:

1. Determine if any required binaries are not in the scale-ci-workload container image
    1. Create a pull request to add the binary to the [workload container image](https://github.com/openshift-scale/images)
2. Determine if any additional workload container images are required
    1. Create a pull request to the [images repo](https://github.com/openshift-scale/images) to add a container image
3. Determine what external variables you will need to populate for your workload
4. Copy and use the nodevertical playbook, vars file, configmap script, and readme as a template
5. Adjust playbook and vars to what your workload needs
6. Replace configmap script with required test/workload
7. Test your workload before opening a pull request

### But my workload also needs a binary/tool/thing or special container image

Create a pull request against the [openshift-scale/images](https://github.com/openshift-scale/images) repo adding the appropriate command to install the tool into the image.  Once the pull request is merged, those container images will be rebuilt by Quay.io and available for consumption.

If you need another container image (For example the uperf or fio container images) also just create a pull request with the files to build your container image into the [openshift-scale/images](https://github.com/openshift-scale/images) repo

### What exactly should the workload script do

Basic workflow for a workload script:

1. Configure pbench agents
2. Perform any pretest configuration
3. Invoke workload script
    1. Invoke with pbench-user-benchmark
    2. Invoke without pbench-user-benchmark
4. Check for Pass/Fail Criteria

Example `run.sh` file that is mounted into scale-ci-workload container image and executed.  Also note the above steps match with comments in below example.

```sh
# (1) Configure pbench agents
#!/bin/sh
set -eo pipefail
workload_log() { echo "$(date -u) $@" >&2; }
export -f workload_log
workload_log "Configuring pbench for Example"
mkdir -p /var/lib/pbench-agent/tools-default/
echo "${USER_NAME:-default}:x:$(id -u):0:${USER_NAME:-default} user:${HOME}:/sbin/nologin" >> /etc/passwd
if [ "${ENABLE_PBENCH_AGENTS}" = true ]; then
  echo "" > /var/lib/pbench-agent/tools-default/disk
  echo "" > /var/lib/pbench-agent/tools-default/iostat
  echo "workload" > /var/lib/pbench-agent/tools-default/label
  echo "" > /var/lib/pbench-agent/tools-default/mpstat
  echo "" > /var/lib/pbench-agent/tools-default/oc
  echo "" > /var/lib/pbench-agent/tools-default/perf
  echo "" > /var/lib/pbench-agent/tools-default/pidstat
  echo "" > /var/lib/pbench-agent/tools-default/sar
  master_nodes=`oc get nodes -l pbench_agent=true,node-role.kubernetes.io/master= --no-headers | awk '{print $1}'`
  for node in $master_nodes; do
    echo "master" > /var/lib/pbench-agent/tools-default/remote@$node
  done
  infra_nodes=`oc get nodes -l pbench_agent=true,node-role.kubernetes.io/infra= --no-headers | awk '{print $1}'`
  for node in $infra_nodes; do
    echo "infra" > /var/lib/pbench-agent/tools-default/remote@$node
  done
  worker_nodes=`oc get nodes -l pbench_agent=true,node-role.kubernetes.io/worker= --no-headers | awk '{print $1}'`
  for node in $worker_nodes; do
    echo "worker" > /var/lib/pbench-agent/tools-default/remote@$node
  done
fi
source /opt/pbench-agent/profile
workload_log "Done configuring pbench Example"

workload_log "Configuring Example"
# (2) Perform any pretest configuration
workload_log "Done configuring Example"

workload_log "Running Example workload"
if [ "${PBENCH_INSTRUMENTATION}" = "true" ]; then
  # (3i) Invoke with pbench-user-benchmark
  pbench-user-benchmark -- sh /root/workload/workload.sh
  result_dir="/var/lib/pbench-agent/$(ls -t /var/lib/pbench-agent/ | grep "pbench-user" | head -1)"/1/sample1
  if [ "${ENABLE_PBENCH_COPY}" = "true" ]; then
    pbench-copy-results --prefix ${EXAMPLE_TEST_PREFIX}
  fi
else
  # (3ii) Invoke without pbench-user-benchmark
  sh /root/workload/workload.sh
  result_dir=/tmp
fi
workload_log "Completed Example workload run"

workload_log "Checking Test Results"
# (4) Check for Pass/Fail Criteria
workload_log "Checking Exit Code"
if [ "$(jq '.exit_code==0' ${result_dir}/exit.json)" = "false" ]; then
  workload_log "Example workload Failure"
  workload_log "Test Analysis: Failed"
  exit 1
fi
workload_log "Comparing Example duration to expected duration"
workload_log "Example Duration: $(jq '.duration' ${result_dir}/exit.json)"
if [ "$(jq '.duration>'${EXPECTED_EXAMPLE_DURATION}'' ${result_dir}/exit.json)" = "true" ]; then
  workload_log "EXPECTED_EXAMPLE_DURATION (${EXPECTED_EXAMPLE_DURATION}) exceeded ($(jq '.duration' ${result_dir}/exit.json))"
  workload_log "Test Analysis: Failed"
  exit 1
fi
# TODO: Check pbench-agent collected metrics for Pass/Fail
# TODO: Check prometheus collected metrics for Pass/Fail
workload_log "Test Analysis: Passed"
```

Example `workload.sh` which is also mounted in the container image.  A separate `workload.sh` script is used for workloads which require `pbench-user-benchmark` and desire the ability to run with and without pbench agent instrumentation as well as pbench results collection.

```sh
#!/bin/sh
set -o pipefail

result_dir=/tmp
if [ "${PBENCH_INSTRUMENTATION}" = "true" ]; then
  result_dir=${benchmark_results_dir}
fi
start_time=$(date +%s)
# Insert Your workload here
sleep 60 # Sleep just for example
exit_code=$?
end_time=$(date +%s)
duration=$((end_time-start_time))

workload_log "Writing Exit Code"
jq -n '. | ."exit_code"='${exit_code}' | ."duration"='${duration}'' > "${result_dir}/exit.json"
workload_log "Finished workload script"
```

## Adding the workload to the scale-ci-pipeline

Each workload if conforming to the above guidelines, should be easy to implement into the scale-ci-pipeline for continuous scale testing of OpenShift releases. Follow this [docs](https://github.com/openshift-scale/scale-ci-pipeline#modifyingadding-new-workloads-to-the-scale-ci-pipeline) to add a job with the new workload once the workload has been merged into the repo.
