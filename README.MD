# MongoDB HA Cluster (Kubernetes) 🚀

Welcome to the **MongoDB High Availability (HA) Cluster** project on Kubernetes! This repository contains all the necessary configuration files and instructions to deploy a robust MongoDB cluster with high availability on a Kubernetes cluster. 

## 📁 Repository Files

Here's an overview of the files in this repository:

- **`pv-pvc.yaml`**: PersistentVolume (PV) and PersistentVolumeClaim (PVC) definitions for MongoDB data storage.
- **`svc.yaml`**: Service configurations to expose MongoDB pods internally within the cluster.
- **`sts.yaml`**: StatefulSet configuration for managing MongoDB pods.
- **`Bkp_pv-pvc.yaml`**: PV and PVC definitions for backup storage.
- **`Bkp_cronjob.yaml`**: CronJob definition to automate MongoDB backups. ⏱️
- **`AccessBkp.yaml`**: Configuration to provide secure access to backup files. 🔒
- **`svc-hl.yaml`**: High-level Service configuration for external access to the MongoDB cluster.

## 📜 Prerequisites

Ensure the following are set up in your environment:

1. **Kubernetes Cluster**: A functioning Kubernetes cluster (v1.20+ recommended).
2. **kubectl**: Kubernetes command-line tool installed and configured.
3. **Storage Class**: A valid storage class configured for PV provisioning.

## 🚀 Deployment Steps

1. **Clone the Repository**:
   ```bash
   git clone https://github.com/srinu7963/MongoDBonK8s.git
   cd mongodb-ha-cluster-k8s
   ```

2. **Apply PV and PVC Configurations**:
   ```bash
   kubectl apply -f pv-pvc.yaml
   ```

3. **Deploy MongoDB StatefulSet**:
   ```bash
   kubectl apply -f sts.yaml
   ```

4. **Expose MongoDB Services**:
   ```bash
   kubectl apply -f svc.yaml
   kubectl apply -f svc-hl.yaml
   ```

5. **Set Up Backups**:
   - Deploy backup storage:
     ```bash
     kubectl apply -f Bkp_pv-pvc.yaml
     ```
   - Schedule automated backups:
     ```bash
     kubectl apply -f Bkp_cronjob.yaml
     ```

6. **Secure Access to Backups**:
   ```bash
   kubectl apply -f AccessBkp.yaml
   ```

## 📊 Monitoring and Maintenance

- Use Kubernetes commands to monitor the deployment:
  ```bash
  kubectl get pods
  kubectl get svc
  ```
- Inspect logs for any issues:
  ```bash
  kubectl logs <pod-name>
  ```

## 🙌 Contributions

Contributions are welcome! Feel free to submit a pull request or open an issue to discuss potential changes.

## 📄 License

This project is licensed under the Anjoori License - see the [LICENSE](LICENSE) file for details.

---

🌟 **Thank you for using this project!** If you find it helpful, give the repository a star to show your support! ⭐

