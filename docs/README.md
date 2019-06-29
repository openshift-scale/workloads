# Table of workloads

| Workload/tooling                                   | Short Description                         | Minimum Requirements                  |
|:-------------------------------------------------- |:----------------------------------------- | ------------------------------------- |
| [Tooling](tooling.md)                              | Setup pbench metric tools                 | Cluster-admin, Privileged Containers  |
| [Test](test.md)                                    | Test/Run your workload from ssh Container | Cluster-admin, Privileged Containers  |
| [Baseline](baseline.md)                            | Baseline metrics capture                  | Tooling job*                          |
| [Scale](scale.md)                                  | Scales worker nodes                       | Cluster-admin                         |
| [NodeVertical](nodevertical.md)                    | Node Kubelet Density                      | Labeling Nodes                        |
| [PodVertical](podvertical.md)                      | Max Pod Density                           | None                                  |
| [MasterVertical](mastervertical.md)                | Master Node Stress workload               | None                                  |
| [HTTP](http.md)                                    | HTTP ingress TPS/Latency                  | None                                  |
| [Network](network.md)                              | TCP/UDP Throughput/Latency                | Labeling Nodes, [See below](#network) |
| [Deployments Per Namespace](deployments-per-ns.md) | Maximum Deployments                       | None                                  |

* Baseline job without a tooled cluster just idles a cluster.  The goal is to capture resource consumption over a period of time to characterize resource requirements thus tooling is required. (For now)

## Network

| Test                       | Requirement                                 |
|:-------------------------- |:------------------------------------------- |
| Pod 2 Pod                  | Labeling Nodes                              |
| Pod 2 Pod with HostNetwork | Labeling Nodes, Open firewall ports at Host |
| Service 2 Service          | Labeling Nodes                              |
