# workloads

Tool an OpenShift cluster and run OpenShift Performance and Scale Workloads.

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

## Usage from CI

(Insert how to create jobs and link to scale-ci-pipeline)

## More Docs

[See docs directory](docs/)
