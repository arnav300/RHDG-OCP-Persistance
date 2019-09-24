# RHDG-OCP-Persistance
File store for RHDG 7.3 running on OCP 3.11

Below steps are required to be followed :

- Make sure your Openshift cluster is up and running.
- We would need PV for this tutorial. Please make sure to create PV and PVC for this demo.
- Before You Begin: Complete the steps in the OpenShift Quickstart README to set up an OpenShift cluster and create Data Grid for OpenShift services.
- Run your datagrid Service pod.

~~~
oc new-app datagrid-service \
  -p APPLICATION_USER=${user} \
  -p APPLICATION_PASSWORD=${password} \
  -p APPLICATION_NAME=${appName}
~~~

In this example I used below command :

~~~
"oc new-app datagrid-service -p APPLICATION_USER=test -p APPLICATION_PASSWORD=changeme -p APPLICATION_NAME=testapp"
~~~

- Make sure that your pods are running without any errors.

~~~
[quicklab@master-0 createcache]$ oc get pvc
NAME                 STATUS    VOLUME    CAPACITY   ACCESS MODES   STORAGECLASS   AGE
srv-data-testapp-0   Bound     vol133    10Gi       RWO                           9m
[quicklab@master-0 createcache]$ oc get pods
NAME        READY     STATUS    RESTARTS   AGE
testapp-0   1/1       Running   0          9m
[quicklab@master-0 createcache]$ 
~~~

##Building the Hello World Quickstart
