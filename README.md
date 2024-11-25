# ClusterCleanup Repository

This repository contains a comprehensive set of tools and workflows for maintaining Kubernetes clusters by automating the cleanup of unused or misconfigured resources. It is designed to optimize cluster performance and resource utilization.

## Authors

This repository was developed by **Dor David Israel** and **Yaron Yadid**. 

---

## Structure

### 1. **Helm Chart: `helm-chart-cluster-cleanup`**

The Helm chart in this repository simplifies the deployment of all cleanup workflows to a Kubernetes cluster. It provides a configurable and centralized way to manage cleanup operations, ensuring efficient resource management through automation.

#### Features:

- **Batch Deployment**: Deploy all cleanup cron workflows simultaneously.
- **Custom Scheduling**: Allows customization of when workflows execute by changing the schedule section of the independent cronworkflow.
- **Selective Activation**: Choose which workflows to deploy by configuring the Helm chart.
- **Parameterization**: Includes options for dry-run mode and resource-specific configurations.
  
#### Workflows Automated by the Helm Chart:

1. **Stale Deployments**: Deletes Deployments with 0 replicas. 
2. **Empty Namespaces**: Removes namespaces without active resources. 
3. **Unbound PVCs**: Deletes PersistentVolumeClaims (PVCs) stuck in the `Pending` state.
4. **Available PVs**: Deletes unused PersistentVolumes in the `Available` state.
5. **Outdated Pods**: Identifies and handles Pods in completed state.
6. **Services without Endpoints**: Cleans up orphaned or unused services.
7. **Ingress Resources**: Removes ingress objects that reference non-existent services.
8. **Non functioning Deployments/StatefulSets**: Identifies pods stuck in the CrashLoopBackOff or ImagePullBackOff state for over a week and scales their owning deployment/StatefulSet to zero.

** note that the execution order of the steps is important because:
1. The cleanup of the ingresses runs after the cleanup of the services so that the ingersses with no endpoints will be deleted.
2. The cleanup of the Non functioning Deployments/StatefulSets runs before scaling to zero Stale Deployments so that in the next run it will delete the objects scaled to zero. 

#### Helm installation 

1. Edit `values.yaml` to suit your configurations and schedules.
2. Deploy the chart using Helm:
   
   $ helm install cluster-cleanup helm-chart-cluster-cleanup/
   
3. Monitor the cron workflows created:
   
   $ kubectl get cronworkflows -n <namespace>
   

---

### 2. **Independent Workflows**

The repository also includes standalone cron workflows in the `independent-cron-workflows` directory. These workflows can be deployed individually for specific cleanup tasks without using the Helm chart.

Each independent workflow addresses a single cleanup task, mirroring the capabilities of the Helm chart.
Deploy an instance of the workflows:
  $ kubectl create -f independent-cron-workflows/<workflow name>
If you are using Argo CLI:
  $ argo submit independent-cron-workflows/<workflow name> --watch

---

## Contributing

Contributions are welcome! Whether it’s adding new workflows, fixing bugs, or improving existing features, your input is valued.

To contribute:

1. Fork the repository.
2. Create a new branch for your changes.
3. Submit a pull request with a clear description of your enhancements.
---

