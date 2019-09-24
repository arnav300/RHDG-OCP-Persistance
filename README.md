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

# Building the Hello World Quickstart :

1. Create a new binary build on OpenShift.

~~~
$ oc new-build --binary --strategy=source --name=quickstart -l app=quickstart fabric8/s2i-java:2.3
~~~

2. Build the Hello World quickstart.
~~~
$ mvn -s ../../../settings.xml clean package compile -DincludeScope=runtime
~~~

Artifacts are built in the target directory.

3. Start the build on OpenShift.
~~~
$ oc start-build quickstart --from-dir=target/ --follow
~~~

# Running the Hello World Quickstart :

1. Invoke cache operations with the quickstart application.

~~~
$ oc run quickstart --image=`oc get is quickstart -o jsonpath="{.status.dockerImageRepository}"` \
    --replicas=1 --restart=OnFailure --env APP_NAME=${appName} --env SVC_DNS_NAME=${appName} --env JAVA_OPTIONS=-ea
~~~
Where ${appName} matches the application name that you specified when you created cache-service or datagrid-service.

In this example, below command is used :

~~~
oc run quickstart --image=`oc get is quickstart -o jsonpath="{.status.dockerImageRepository}"` --replicas=1 --restart=OnFailure --env APP_NAME=testapp  --env CMD=get-cache  --env JAVA_OPTIONS=-ea
~~~

2. Verify the cache operations completed successfully.
~~~
$ oc logs quickstart-${id} --tail=50
--- Connect to datagrid-service ---
...
--- Store key='hello'/value='world' pair ---
...
--- Retrieve key='hello' ---
--- Value is 'world' ---
~~~
Where ${id} is the unique ID for the pod. TIP: Use oc get pods to find the pod name.

The preceding log messages show the Hello World quickstart connected to the Data Grid for OpenShift service and stored a hello/world key/value pair. The quickstart application also performs an assertion to ensure that the returned value is world.

You've successfully completed this tutorial!

Do one of the following:

Delete quickstart resources and continue using the project with RHDG for OpenShift.

$ oc delete all --selector=run=quickstart || true
$ oc delete imagestream quickstart || true
$ oc delete buildconfig quickstart || true
Delete the project to remove all resources, for example:

$ oc delete project my_project
