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
- **Custom Scheduling**: Allows customization of when workflows execute using `values.yaml`.
- **Selective Activation**: Choose which workflows to deploy by configuring the Helm chart.
- **Parameterization**: Includes options for dry-run mode, namespace scoping, and resource-specific configurations.
  
#### Workflows Automated by the Helm Chart:

1. **Stale Deployments**: Deletes Deployments with 0 replicas. 
2. **Unbound PVCs**: Deletes PersistentVolumeClaims (PVCs) stuck in the `Pending` state.
3. **Empty Namespaces**: Removes namespaces without active resources.
4. **Services without Endpoints**: Cleans up orphaned or unused services.
5. **Available PVs**: Deletes unused PersistentVolumes in the `Available` state.
6. **Outdated Pods**: Identifies and handles completed pods lingering in a "Succeeded" state.
7. **Pending Pods**: Identifies and handles completed pods lingering in a "ImagePullBackOff" state.
8. **Ingress Resources**: Removes ingress objects that reference non-existent services.
9. **Non functioning Deployments/StatefulSets**: Identifies pods stuck in the CrashLoopBackOff state for over a week and scales their owning deployment/StatefulSet to zero.

#### Installation

1. Edit `values.yaml` to suit your configurations and schedules.
2. Deploy the chart using Helm:
   ```bash
   helm install cluster-cleanup helm-chart-cluster-cleanup/
   ```
3. Monitor the cron workflows created:
   ```bash
   kubectl get cronworkflows -n <namespace>
   ```

---

### 2. **Independent Workflows**

The repository also includes standalone cron workflows in the `independent-cron-workflows` directory. These workflows can be deployed individually for specific cleanup tasks without using the Helm chart.

Each independent workflow addresses a single cleanup task, mirroring the capabilities of the Helm chart.

---

## Contributing

Contributions are welcome! If you would like to add new workflows, fix bugs, or enhance features, feel free to fork the repository and open a pull request.

---

