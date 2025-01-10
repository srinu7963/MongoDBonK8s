# MongoDB HA Helm Chart

This Helm chart deploys a MongoDB High Availability (HA) cluster on Kubernetes with a replica set configuration.

## Prerequisites

- Kubernetes 1.19+
- Helm 3.0+
- PV provisioner support in the underlying infrastructure
- kubectl configured to communicate with your Kubernetes cluster

## Installation

1. Create the MongoDB keyfile secret (required for replica set authentication):
```bash
openssl rand -base64 756 > keyfile
kubectl create secret generic mongodb-keyfile --from-file=keyfile
rm keyfile
```

2. Install the chart:
```bash
# Clone the repository
git clone https://github.com/srinu7963/MongoDBonK8s.git
cd MongoDBonK8s

# Install the chart with default values
helm install mongodb ./mongodb-ha

# Or install with custom values
helm install mongodb ./mongodb-ha --values custom-values.yaml
```

## Configuration

The following table lists the configurable parameters of the MongoDB chart and their default values:

| Parameter | Description | Default |
|-----------|-------------|---------|
| `mongodb.replicas` | Number of replicas in the cluster | `3` |
| `mongodb.image.repository` | MongoDB image repository | `mongo` |
| `mongodb.image.tag` | MongoDB image tag | `latest` |
| `mongodb.storage.size` | Storage size for each MongoDB pod | `5Gi` |
| `mongodb.storage.hostPath` | Host path for persistent storage | `/data/mongodb` |
| `mongodb.service.port` | MongoDB service port | `27017` |
| `mongodb.resources.requests.cpu` | CPU request per pod | `100m` |
| `mongodb.resources.requests.memory` | Memory request per pod | `256Mi` |
| `mongodb.resources.limits.cpu` | CPU limit per pod | `500m` |
| `mongodb.resources.limits.memory` | Memory limit per pod | `512Mi` |

## Post-Installation Setup

1. Wait for all pods to be ready:
```bash
kubectl get pods -w
```

2. Initialize the replica set. Connect to the first pod:
```bash
kubectl exec -it mongodb-0 -- mongo
```

3. In the mongo shell, run:
```javascript
rs.initiate({
  _id: "rs0",
  members: [
    { _id: 0, host: "mongodb-0.mongodb-service:27017" },
    { _id: 1, host: "mongodb-1.mongodb-service:27017" },
    { _id: 2, host: "mongodb-2.mongodb-service:27017" }
  ]
})
```

4. Verify the replica set status:
```javascript
rs.status()
```

## Accessing MongoDB

- Within the cluster:
  ```
  mongodb-service:27017
  ```
- From an application, use the following connection string:
  ```
  mongodb://mongodb-0.mongodb-service:27017,mongodb-1.mongodb-service:27017,mongodb-2.mongodb-service:27017/?replicaSet=rs0
  ```

## Scaling

To scale the cluster:
```bash
helm upgrade mongodb ./mongodb-ha --set mongodb.replicas=5
```

Note: When scaling, you'll need to reconfigure the replica set to include new members.

## Uninstallation

To uninstall/delete the deployment:
```bash
helm uninstall mongodb
```

Note: PersistentVolumes and PersistentVolumeClaims are not automatically deleted. To clean up:
```bash
kubectl delete pvc -l app.kubernetes.io/instance=mongodb
kubectl delete pv -l app.kubernetes.io/instance=mongodb
```

## Troubleshooting

1. Check pod status:
```bash
kubectl get pods
kubectl describe pod mongodb-0
```

2. View logs:
```bash
kubectl logs mongodb-0
```

3. Common issues:
   - If pods are stuck in Pending state, check PV/PVC status
   - If replica set initialization fails, ensure all pods are running
   - For authentication issues, verify the keyfile secret is properly created

## Security Considerations

- The chart uses a keyfile for internal authentication
- Ensure proper network policies are in place
- Consider enabling MongoDB authentication for client connections
- Use secrets for storing sensitive configuration

## Support

For issues and feature requests, please file an issue in the [GitHub repository](https://github.com/srinu7963/MongoDBonK8s/issues).