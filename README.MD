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

The Steps: 

MongoDb E2E with HA:
 
 
mkdir mongoDb1
cd mongoDb1/
kubectl get nodes
 
kubectl create ns mongo
 
vi pv_pvc.yaml
kubectl apply -f pv_pvc.yaml -n mongo
 
kubectl get pvc
kubectl get pv
 
vi mongosv.yaml
kubectl apply -f mongosv.yaml -n mongo
kubectl get svc -n mongo
 
bash -c "openssl rand -base64 756 > mongodb-keyfile"
ls -lrtha
sudo chmod 400 mongodb-keyfile
ls -lrtha
more mongodb-keyfile
 
kubectl create secret generic mongodb-keyfile --from-file=mongodb-keyfile -n mongo
 
vi mongo_sfs.yaml
kubectl apply -f mongo_sfs.yaml -n mongo
kubectl get svc -n mongo
 
kubectl exec -it mongodb-0 -n mongo -- mongosh
 
kubectl exec -it mongodb-0 -n mongo -- mongosh
 
vi backup_pv_pvc.yaml
kubectl apply -f backup_pv_pvc.yaml -n mongo
 
kubectl get pv -n mongo
kubectl get pvc -n mongo
 
vi mongodb_bkup_cron.yaml
kubectl apply -f mongodb_bkup_cron.yaml -n mongo
kubectl get cronjob -nmongo
 
vi access_bkup.yaml
kubectl apply -f access_bkup.yaml -n mongo
 
kubectl exec -it backup-access -n mongo -- /bin/sh
 
kubectl exec -it mongodb-0 -n mongo -- mongosh --eval "rs.status()"
 
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
 
kubectl edit deployment metrics-server -n kube-system
kubectl edit deployment metrics-server -n kube-system
 
kubectl top pods -n mongo
 
 
kubectl autoscale statefulset mongodb --min=3 --max=10 --cpu-percent=50 -n mongo
kubectl get hpa -n mongo
kubectl get all -n mongo

 
===================================================
 
troubleshoot & restart whole...
 

kubectl get hpa -n mongo
kubectl delete -f pv_pvc.yaml
kubectl apply -f pv_pvc.yaml -n mongo
kubectl apply -f mongosv.yaml -n mongo
kubectl apply -f mongo_sfs.yaml -n mongo
kubectl get all -n mongo
kubectl scale statefulset mongodb -n mongo --replicas=0
kubectl delete pvc -n mongo -l app=mongodb
kubectl scale statefulset mongodb -n mongo --replicas=3
kubectl get pods -n mongo
kubectl exec -it mongodb-0 -n mongo -- mongosh
rs.initiate({
  _id: "rs0",
  version: 1,
  members: [
    { _id: 0, host: "mongodb-0.mongodb-service.mongo.svc.cluster.local:27017" },
    { _id: 1, host: "mongodb-1.mongodb-service.mongo.svc.cluster.local:27017" },
    { _id: 2, host: "mongodb-2.mongodb-service.mongo.svc.cluster.local:27017" }
  ]
})
 
 
rs0 [direct: primary] test> rs.status()


====================================
 
Restoring backups:
kubectl cp 2025-01-25T00-00-03/ mongodb-0:/data/db/backup -n mongo
 
kubectl delete hpa mongodb -n mongo
kubectl autoscale statefulset mongodb --min=1 --max=10 --cpu-percent=50 -n mongo
kubectl scale statefulset mongodb -n mongo --replicas=1
kubectl patch hpa mongodb -n mongo -p '{"spec":{"minReplicas":1}}'
 
kubectl exec -it mongodb-0 -n mongo -- /bin/bash
 
mongorestore --host localhost --port 27017 /data/db/backup
or
mongorestore --host localhost --port 27017 --username <username> --password <password> /data/db/backup

