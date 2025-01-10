kubectl create secret generic mongodb-keyfile --from-file=mongodb-keyfile



kubectl exec -it mongodb-0 -- mongosh

rs.initiate({
  _id: "rs0",
  members: [
    { _id: 0, host: "mongodb-0.mongodb-service:27017", priority: 2 },
    { _id: 1, host: "mongodb-1.mongodb-service:27017", priority: 1 },
    { _id: 2, host: "mongodb-2.mongodb-service:27017", priority: 1 }
  ]
})



=============

Begin by identifying the current primary node:
kubectl exec -it mongodb-0 -- mongosh --eval "rs.status()"

Look for the member with the "stateStr" : "PRIMARY" attribute:
{
  "_id" : 0,
  "name" : "mongodb-0.mongodb-service:27017",
  "stateStr" : "PRIMARY",
  ...
}

Simulate a failure by deleting the current primary node pod:
kubectl delete pod mongodb-0

Monitor the status of the replica set and observe the election of a new primary:
kubectl exec -it mongodb-1 -- mongosh --eval "rs.status()"

You should see that one of the secondary nodes has been promoted to primary:
{
  "_id" : 1,
  "name" : "mongodb-1.mongodb-service:27017",
  "stateStr" : "PRIMARY",
  ...
}

Verify that the deleted pod has been recreated and rejoined the replica set as a secondary node:
kubectl get pods

Confirm that the pod is back and running:
NAME                       READY   STATUS     RESTARTS     	AGE
mongodb-0                       1/1     Running     0         		 70s
mongodb-1                       1/1     Running     0          		77m
mongodb-2                       1/1     Running     0         		 73m

Check the replica set status again to ensure the new node is now re-elected as the primary since it had the highest priority:
kubectl exec -it mongodb-0 -- mongosh --eval "rs.status()"

You should see:
{
  "_id" : 0,
  "name" : "mongodb-0.mongodb-service:27017",
  "stateStr" : "PRIMARY",
  ...
}


==========

For AutoScaling :

kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml


kubectl autoscale statefulset mongodb --min=3 --max=10 --cpu-percent=50


