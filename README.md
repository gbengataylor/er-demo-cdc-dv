prereq: install er-demo

Create a project called datavirt

```sh
#change namespace if needed
erdemometricsnamespace=user1-er-metrics
oc new-project datavirt
```

install the Red Hat Data Virtualization operator in datavirt project via OperatorHub

modify the er-vdb.yaml file. Substitue occurrences of 'user1-er-demo' with userX-er-demo, where X is the user that has er-demo installed. There are three occurences in the file related to the jdbc connection string. For e.g

```yaml

apiVersion: teiid.io/v1alpha1
kind: VirtualDatabase
metadata:
  name: ervdb
...
    - name: er
      type: postgresql
      properties:
        - name: username
          value: naps
        - name: password
          value: naps
        - name: jdbc-url
          value: jdbc:postgresql://postgresql.user1-er-demo.svc.cluster.local:5432/emergency_response_demo <--update with appropriate namespace
          
````

add the VDB

```sh
# first time will take about 10 mins to create vdbbuilder (one time only)
# actual vdb will build in about 3 mins
oc apply -f er-vdb.yaml -n datavirt
# if you don't clone the repo and user matches
# oc appy -f https://raw.githubusercontent.com/gbengataylor/er-demo-cdc-dv/master/er-vdb.yaml -n datavirt
```

install the datawarehouse in the er demo namespace

```sh
oc apply -f grafana_vdbdatasources.yaml -n $erdemometricsnamespace
# if you don't clone the repo and user matches
#oc apply -f https://raw.githubusercontent.com/gbengataylor/er-demo-cdc-dv/master/grafana_vdbdatasources.yaml -n $erdemometricsnamespace
```

Modify grafana-mission-commander-kpi-vdb-dashboard.yaml  and modify the label of the CR 

```yaml
apiVersion: integreatly.org/v1alpha1
kind: GrafanaDashboard
metadata:
  labels:
    monitoring-key: user1-application-monitoring <--- update with user
  name: mission-commander-vdb-kpis
```

Add the dashboard

```sh
oc apply -f grafana-mission-commander-kpi-vdb-dashboard.yaml -n $erdemometricsnamespace
# if you don't clone the repo and user matches
#oc apply -f https://raw.githubusercontent.com/gbengataylor/er-demo-cdc-dv/master/grafana-mission-commander-kpi-vdb-dashboard.yaml -n $erdemometricsnamespace
```



