prereq: install er-demo

Create a project called datavirt

```sh
oc new-project datavirt
```

install the Red Hat Data Virtualization operator in datavirt project via OperatorHub

modify the er-vdb.yaml file. substitue occurrences user10-er-demo with userX-er-demo, where X is the user that has er-demo installed. There are three instances. For e.g

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
          value: jdbc:postgresql://postgresql.user10-er-demo.svc.cluster.local:5432/emergency_response_demo <--update with appropriate namespace
          
````

add the VDB

```sh
# first time will take about 10 mins to create vdbbuilder (one time only)
# actual vdb will build in about 3 mins
oc apply -f er-vdb.yaml -n datavirt
```

install the datawarehouse in the er demo namespace

```sh
oc apply -f grafana_vdbdatasources.yaml -n $er-demo-namespace
```

Modify grafana-mission-commander-kpi-vdb-dashboard.yaml  and modify the label of the CR 

```yaml
apiVersion: integreatly.org/v1alpha1
kind: GrafanaDashboard
metadata:
  labels:
    monitoring-key: user10-application-monitoring <--- update with user
  name: mission-commander-vdb-kpis
```

Add the dashboard

```sh
oc apply -f grafana-mission-commander-kpi-vdb-dashboard.yaml -n $er-demo-namespace
```



