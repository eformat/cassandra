# cassandra
Deploying Clustered Cassandra on OpenShift

## Manual method (OpenShift versions prior to v3.7)

## StatefulSets method (tech preview in v3.7)

### (3) existing 1Gi PVs are required.

### Create a project
oc new-project statefuldead

### Run the following as cluster-admin
oc adm policy add-scc-to-user privileged -z default -n $(oc project -q)

### Create the Service and StatefulSet

oc create -f cassandra-service.yaml
oc create -f cassandra-statefulset.yaml

### Wait for (3) pods to become ready and check the cluster status.

```
$ oc get pods

NAME          READY     STATUS    RESTARTS   AGE
cassandra-0   1/1       Running   0          1h
cassandra-1   1/1       Running   0          59m
cassandra-2   1/1       Running   0          58m

$ oc rsh cassandra-0 nodetool status

Datacenter: DC1-K8Demo
======================
Status=Up/Down
|/ State=Normal/Leaving/Joining/Moving
--  Address      Load       Tokens       Owns (effective)  Host ID                               Rack
UN  172.17.0.9   83.54 KiB  32           63.0%             68fc41fd-3d3f-435a-bbd4-50e9869ba0ba  Rack1-K8Demo
UN  172.17.0.8   127.51 KiB  32           61.0%             81d63634-7161-4042-8f4e-c0aaff465f16  Rack1-K8Demo
UN  172.17.0.10  84.71 KiB  32           75.9%             68489417-2864-4a42-9897-e31433265407  Rack1-K8Demo
```

