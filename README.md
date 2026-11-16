# Nginx Release Management with Helm



Created a custom Helm chart to deploy Nginx on Docker Desktop Kubernetes, scale the application, and restore an earlier release configuration.



## Environment



- Windows with Git Bash

- Docker Desktop Kubernetes: one local node

- Helm v4.3.1

- Nginx 1.25.3, matching the class exercise



## Chart Structure



- `Chart.yaml`: chart metadata

- `values.yaml`: default application settings

- `templates/deployment.yaml`: Nginx Deployment

- `templates/service.yaml`: internal ClusterIP Service

- `templates/_helpers.tpl`: reusable naming helpers



## Work Completed



- Created and customized a Helm chart.

- Passed `helm lint` validation.

- Inspected generated manifests with `helm template`.

- Installed the release and verified the Nginx welcome page through port forwarding.

- Scaled from one to two replicas by editing values.yaml.

- Scaled to three replicas using a command-line override.

- Rolled back to the original one-replica configuration.

- Verified Deployment readiness, pod health, and Service endpoints.

- Packaged the chart for reuse.



## Verified Release History



| Revision | Action | Verified replicas |

| --- | --- | --- |

| 1 | Initial installation | 1 |

| 2 | Upgrade using values.yaml | 2 |

| 3 | Upgrade using --set | 3 |

| 4 | Rollback to revision 1 | 1 |



Rollback created revision 4 using revision 1's configuration. It did not edit local files. The local chart defaults were subsequently returned to one replica.



## Evidence Files



- `helm-release-history.txt`

- `workload-validation.txt`

- `service-validation.txt`

- `endpoint-validation.txt`

- `nginx-chart-0.1.0.tgz`



The release history retains the starter chart's appVersion of 1.16.0. The actual deployed image was nginx:1.25.3, confirmed in workload-validation.txt. Chart metadata was corrected before packaging.



## Reproduce the Deployment



Confirm that kubectl targets the intended Kubernetes cluster before installing.



```bash

helm lint nginx-chart

helm template my-nginx ./nginx-chart

helm install my-nginx ./nginx-chart --wait --timeout 3m

kubectl get deployment,pods -l app=nginx-chart

kubectl get service my-nginx-nginx-chart

kubectl port-forward svc/my-nginx-nginx-chart 8080:80

```



Open http://localhost:8080 to view Nginx. Press Ctrl+C in the port-forward terminal when finished.



## Practice Scaling and Rollback



```bash

helm upgrade my-nginx ./nginx-chart --set replicaCount=2 --wait --timeout 3m

helm upgrade my-nginx ./nginx-chart --set replicaCount=3 --wait --timeout 3m

helm history my-nginx

helm rollback my-nginx 1 --wait --timeout 3m

kubectl get deployment,pods -l app=nginx-chart

```



## Cleanup



Cleanup verification is pending. The release was installed when the evidence was captured.



```bash

helm uninstall my-nginx --wait --timeout 3m

```



## Scope and Limitations



This is a local learning demonstration, with all pods running on one node. Multiple replicas do not protect against that node failing.



The chart uses an older Nginx image to reproduce the class exercise. Review and update the image before using it elsewhere.



The simplified chart shares its app selector across installations. Use one release per namespace to avoid overlapping selectors.



## Skills Demonstrated



Helm chart authoring, YAML configuration, template rendering, Kubernetes Deployments and Services, scaling, release history, rollback, verification, and chart packaging.

