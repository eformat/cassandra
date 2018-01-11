# cassandra
Deploying Clustered Cassandra on OpenShift

## Manual method (OpenShift versions prior to v3.7)
```oc new-project cassandra```

As cluster admin, make the following policy change:
```oc adm policy add-scc-to-user anyuid -z default -n cassandra```

Create (3) apps.

```oc new-app --name=cassandra-0 docker.io/cassandra -e CASSANDRA_BROADCAST_ADDRESS=cassandra-0.cassandra.svc.cluster.local```

```oc new-app --name=cassandra-1 docker.io/cassandra -e CASSANDRA_SEEDS=cassandra-0.cassandra.svc.cluster.local CASSANDRA_BROADCAST_ADDRESS=cassandra-1.cassandra.svc.cluster.local```

```oc new-app --name=cassandra-2 docker.io/cassandra -e CASSANDRA_SEEDS=cassandra-0.cassandra.svc.cluster.local CASSANDRA_BROADCAST_ADDRESS=cassandra-2.cassandra.svc.cluster.local```

Proceed to checking the cluster status.

## StatefulSets method (tech preview in v3.7)

Based on an [example](https://kubernetes.io/docs/tutorials/stateful-application/cassandra/)
from the Kubernetes tutorials.

(3) existing 1Gi PVs are required.

Create a project

``` oc new-project statefuldead ```

As cluster admin, make the following policy change:

```oc adm policy add-scc-to-user anyuid -z default -n cassandra```

Create the Service and StatefulSet from a template.

```oc process -f https://raw.githubusercontent.com/bkoz/cassandra/master/cassandra-cluster.yaml | oc create -f -```

#### Checking the cluster status.

Wait for (3) cassandra pods to become ready. 

```oc get pods```

```
NAME          READY     STATUS    RESTARTS   AGE
cassandra-0   1/1       Running   0          1h
cassandra-1   1/1       Running   0          59m
cassandra-2   1/1       Running   0          58m
```
```oc rsh cassandra-0 nodetool status```
```
Datacenter: DC1-K8Demo
======================
Status=Up/Down
|/ State=Normal/Leaving/Joining/Moving
--  Address      Load       Tokens       Owns (effective)  Host ID                               Rack
UN  172.17.0.9   83.54 KiB  32           63.0%             68fc41fd-3d3f-435a-bbd4-50e9869ba0ba  Rack1-K8Demo
UN  172.17.0.8   127.51 KiB  32           61.0%             81d63634-7161-4042-8f4e-c0aaff465f16  Rack1-K8Demo
UN  172.17.0.10  84.71 KiB  32           75.9%             68489417-2864-4a42-9897-e31433265407  Rack1-K8Demo
```
#### Clean up
```oc delete all,pvc --all```

