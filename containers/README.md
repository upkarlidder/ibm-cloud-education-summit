# Running Your Docker Containers in Kubernetes in IBM Cloud

## Sample Application

The sample application is a status page application that lets you record system status.

## Deploying Your First App
Step 1: Clone Repo

`git clone https://gitlab.com/ibm/kube101`

This contains the application code for the python app, as well as all the kubernetes configurations you’ll need.

## Step 2: Build Image

First, we have to login to our environment:

```
ibmcloud login --sso
ibmcloud cr login
cd kube101/

ibmcloud cr build --tag us.icr.io/kubeworkshop101/$YOURNAME-status-page:1 status_page
```

**The Application Image**

The following is the image file that we’re building.

```
FROM ubuntu:18.04

ENV DEBIAN_FRONTEND noninteractive
ENV LANG C.UTF-8
ENV LC_ALL C.UTF-8
ENV FLASK_APP status_page
ENV STATUS_PAGE_SETTINGS ../settings.cfg

RUN apt-get update && apt-get install -y python3 python3-dev python3-pip && apt-get clean

COPY ./ /var/www/status_page
WORKDIR /var/www/status_page

RUN pip3 install -U .

CMD flask run -h 0.0.0.0
```


This has a few basic container image stanzas:

 - FROM - specify a base image, in our case a dockerhub ubuntu 18.04 minimal image
 - ENV - specify environment variables. Many are needed for apt to run cleanly
 - RUN - run a command. We do an apt install, as well as a pip install later
 - COPY - copy files from the local directory into the image. This is how we load in our application.
 - WORKDIR - set the working directory for the image
 - CMD - what command should we run if nothing else is specified via kubernetes.

It is important that flask is passed the `-h 0.0.0.0` argument. Without that, it would bind to localhost (i.e. 127.0.0.1), which would not allow inbound connections.

## Step 3: Connect to Kube Cluster

Run the following command: 

`ibmcloud ks cluster-config $yourclustername`

Which returns something like

```
OK

The configuration for yourclustername was downloaded successfully. Export
environment variables to start using Kubernetes.

export KUBECONFIG=/home/mareksadowski/.bluemix/plugins/container-service/clusters/yourclustername/kube-config-wdc06-yourclustername.yml
```

You must then run the export command to enable kubectl to access your cluster. For the rest of this exercise we’ll be using kubectl for almost all actions.

## Step 4: Explore the cluster

A good starting point for the cluster is to look at all the resources:

```
kubectl get all -o wide

NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE       SELECTOR
service/kubernetes   ClusterIP   172.21.0.1   <none>        443/TCP   10d       <none>
```

A kuberenetes cluster starts with very little in it’s default namespace. There is just a single service for the kubernetes API itself.

## Step 5: Deploying the Application

Edit the deploy/status-deployment.yaml file and replace status_page with your chosen `$YOURNAME-status-page:`

```
vi deploy/status-deployment.yaml
# edit line 19 with $YOURNAME

...
    spec:
      containers:
      - name: status-web
        image: us.icr.io/kubeworkshop101/$YOURNAME-status-page:1
        imagePullPolicy: Always
...
```

Then you can deploy the application:

```
kubectl apply -f deploy/status-deployment.yaml

deployment.apps "status-web" created
service "status-web" created
```

Then look at what happened:

```
kubectl get all -o wide

NAME                              READY     STATUS    RESTARTS   AGE       IP              NODE
pod/status-web-64474bccd5-btmn5   0/1       Running   0          33s       172.30.112.86   10.190.15.245
pod/status-web-64474bccd5-fwt5h   0/1       Running   0          33s       172.30.112.87   10.190.15.245
pod/status-web-64474bccd5-rjq44   0/1       Running   0          33s       172.30.112.85   10.190.15.245

NAME                 TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE       SELECTOR
service/kubernetes   ClusterIP   172.21.0.1       <none>        443/TCP          10d       <none>
service/status-web   NodePort    172.21.161.127   <none>        5000:32101/TCP   33s       app=status-web

NAME                         DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE       CONTAINERS   IMAGES                                      SELECTOR
deployment.apps/status-web   3         3         3            0           33s       status-web   registry.ng.bluemix.net/status_page/web:1   app=status-web

NAME                                    DESIRED   CURRENT   READY     AGE       CONTAINERS   IMAGES                                      SELECTOR
replicaset.apps/status-web-64474bccd5   3         3         0         33s       status-web   registry.ng.bluemix.net/status_page/web:1   app=status-web,pod-template-hash=2003067781
```

**Connect to Application**

Once the application is deployed, we can connect to it. Because we are using a NodePort we need to run a couple of commands to determine the url for the application.

```
kubectl get nodes -o wide

NAME            STATUS    ROLES     AGE       VERSION       EXTERNAL-IP    OS-IMAGE             KERNEL-VERSION      CONTAINER-RUNTIME
10.190.15.245   Ready     <none>    10d       v1.10.8+IKS   169.61.97.62   Ubuntu 16.04.5 LTS   4.4.0-137-generic   docker://17.6.2
```

The import information here is the EXTERNAL-IP.

```
kubectl get service -l app=status-web -o wide

NAME         TYPE       CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE       SELECTOR
status-web   NodePort   172.21.161.127   <none>        5000:32101/TCP   13m       app=status-web
```

Here we need the 2nd port listed under ports. That’s what’s exposed to the outside world.

The above would give us a URL of http://169.61.97.62:32101.

**Did it work?**
Discovering what’s going on

Run the following command again:

```
kubectl get pod -l app=status-web -o wide

NAME                          READY     STATUS    RESTARTS   AGE       IP              NODE
status-web-64474bccd5-btmn5   0/1       Running   0          18m       172.30.112.86   10.190.15.245
status-web-64474bccd5-fwt5h   0/1       Running   0          18m       172.30.112.87   10.190.15.245
status-web-64474bccd5-rjq44   0/1       Running   0          18m       172.30.112.85   10.190.15.245
```

What’s going on in that READY field? Why aren’t any of our services ready?

Let’s start with looking at one of the pods and see if we can see:

```
kubectl describe pod/status-web-64474bccd5-rjq44

...
Events:
  Type     Reason                 Age                 From                    Message
  ----     ------                 ----                ----                    -------
  Normal   Scheduled              21m                 default-scheduler       Successfully assigned status-web-64474bccd5-rjq44 to 10.190.15.245
  Normal   SuccessfulMountVolume  21m                 kubelet, 10.190.15.245  MountVolume.SetUp succeeded for volume "default-token-hsk5t"
  Normal   Pulling                21m                 kubelet, 10.190.15.245  pulling image "registry.ng.bluemix.net/status_page/web:1"
  Normal   Pulled                 21m                 kubelet, 10.190.15.245  Successfully pulled image "registry.ng.bluemix.net/status_page/web:1"
  Normal   Created                21m                 kubelet, 10.190.15.245  Created container
  Normal   Started                21m                 kubelet, 10.190.15.245  Started container
  Warning  Unhealthy              1m (x119 over 21m)  kubelet, 10.190.15.245  Readiness probe failed: HTTP probe failed with statuscode: 500
```

Ah, **we’re failing a readiness probe**. Because we’re failing readiness, the pods in question aren’t being added to the service pool, and thus there is nothing to answer the incoming requests.

If we **look at our deployment yaml** we’ll see that we included a readiness check

```
...
    spec:
      containers:
      - name: status-web
        image: us.icr.io/kubeworkshop101/$YOURNAME-status-page:1
        imagePullPolicy: Always
        env:
          - name: REDIS_HOST
            value: "redis-leader"
        ports:
        - name: http
          containerPort: 5000
          protocol: TCP
        readinessProbe:
          httpGet:
            path: /readiness
            port: 5000
```

The reason the readiness probe is failing is that the redis datastore that's needed for the application hasn't been deployed. We can fix that in our next step.

