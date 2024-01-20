![Ali Amirshahi Banner](https://i.ibb.co/2WKr9HR/github-banner-small.png)

<a href="https://github.com/ALPHYPSYCHE/Kubernetes">
    <div style="margin-bottom:1em;"> 
        <img style="margin-right:-.2em;" align="left" src="https://cdn.worldvectorlogo.com/logos/kubernets.svg" title="Kubernetes" width="100" height="100"/>
    </div>
    <div style="margin-bottom:-1.5em;">
        <h1 display="display:inline;">
            <font size="+4">Kubernetes</font>
        </h1>
    </div>
</a>

<div style="margin-left:5em;">
    <span style="vertical-align: middle;"><font size="+2">Kubernetes, often abbreviated as K8s, is an open-source container orchestration platform that automates the deployment, scaling, and management of containerized applications. Developed by Google and later donated to the Cloud Native Computing Foundation (CNCF), Kubernetes provides a robust and flexible solution for container orchestration, allowing organizations to efficiently manage complex microservices architectures. At its core, Kubernetes abstracts the underlying infrastructure, enabling seamless deployment and operation of applications across diverse environments. Its key features include automated load balancing, self-healing capabilities, rolling updates, and declarative configuration, empowering developers and system administrators to effectively manage container workloads. Widely adopted in the industry, Kubernetes has become the de facto standard for container orchestration, fostering innovation, scalability, and portability in modern cloud-native application development.</font></span>
</div>
<br>
<br>
<p align="center">
  <img src="https://readme-typing-svg.demolab.com/?lines=Welcome+to+kubernetes+page;Not+completed+yet!;Will+add+more+parts+in+the+future!;Will+complete+soon!&font=Fira%20Code&center=true&width=600&height=50&duration=4000&pause=1000">
</p>

## 📚 Table of Contents

1. [ETCD](#etcd)
2. [KUBE API-SERVER](#kube-api-server)
3. [SCHEDULER](#scheduler)
4. [SCHEDULING](#scheduling)
5. [KUBELET](#kubelet)
6. [KUBE-PROXY](#kube-proxy)
7. [PODS](#pods)
8. [REPLICATION CONTROLLER](#replication-controller)
9. [DEPLOYMENTS](#deployments)
10. [NAMESPACE](#namespace)
11. [SERVICES](#services)
12. ...

### LETS START STEP BY STEP.

## ETCD

ETCD is a distributed key-value store used as the primary data store in Kubernetes clusters. It maintains the configuration data, ensuring consistency and reliability across multiple nodes, facilitating coordination among various components.

Setup Kubeadm:
```bash
kubectl get pods -n kube-system
```

## KUBE API-SERVER

The Kubernetes API server acts as the control plane's front-end, handling API requests, authentication, and authorization. It validates and processes requests to ensure the desired state of the cluster, interacting with the ETCD store and initiating changes in response to user or controller actions.

Kubernetes Architecture:
```bash
kubectl get nodes
curl -X POST / api/v1/namespace/default/pods ...[others]
```
Install Kube API-Server:
```bash
wget https://storage.googleapis.com/kubernetes-release/release/v1.13.0/bin/linux/amd64/kube-apiserver
```
View API-Server Options :
```bash
cat /etc/systemd/system/kube-apiserver.service
```
Running Process / Effective:
```bash
ps -aux | grep kube-apiserver
```
View API-Server Kubeadm:
```bash
kubectl get pods -n kube-system
```
View API-Server Options Kubeadm:
```bash
cat /etc/kubernetes/manifests/kube-apiserver.yaml
```
## SCHEDULER

The Kubernetes Scheduler is responsible for assigning work (pods) to nodes based on resource availability, constraints, and policies. It constantly evaluates the cluster state and deploys pods to appropriate nodes, optimizing resource utilization and maintaining high availability.

View scheduler Options :
```bash
cat /etc/kubernetes/manifests/kube-scheduler.yaml
```
scheduler Running Process:
```bash
ps -aux | grep kube-scheduler 
```

## SCHEDULING


in pod-definition.yml under spec, every pod has a field called "nodeName" thay by default is not set.

```bash
apiVersion: v1
kind: pod
metadata:
  name: nginx
  labels:
    name:nginx

spec:
  containers:
  - name:nginx
  image: nginx
  ports:
  - containerPort: 8080

  nodeName: node02
```

the scheduler goes through all the pods & looks for those that do not have this property set. those are the candidates for scheduling.
if there is no scheduler to monitor & schedule nodes, the pods status continue to be in a pending state. you can manually assign pods to node with "nodeName".you must do this at the creation time of the pod.
if the pod is existingyou must create binding object & send a post request to the pod binding API & set the binding object in a JSON format. we creat Pod-binding-definition.yaml

```bash
apiVersion: v1
kind: binding
metadata:
  name: nginx
target:
  apiVersion: v1
  kind: Node
  name: node02
```

then send a post request to the pod binding API:

```bash
curl --header "Content-Type:application/json" --request POST --data  '{"application":"v1","kind":"Binding", ....}
http://$SERVER/api/v1/namespaces/default/pods/$PODNAME/binding/
```
you must convert the YAML file into its equivalent JSON format.

## KUBELET

Kubelet is a critical component on each node, ensuring that containers within pods are running and healthy. It communicates with the API server, receives pod specifications, and takes necessary actions to maintain the desired state, such as starting, stopping, or restarting containers.

Setup kubelet (allways install kubelet manually in the worker nodes)
```bash
wget https://storage.googleapis.com/kubernetes-release/release/v1.13.0/bin/linux/amd64/kubelet
```
Kubelet Running Process:
```bash
ps -aux | grep Kubelet 
```

## KUBE-PROXY

Kube-Proxy is responsible for network communication within the cluster. It maintains network rules on nodes, allowing communication between pods and ensuring network connectivity. Kube-Proxy plays a crucial role in service discovery and load balancing.

POD : pod network is an internal virtual network that spans across all the nodes in the cluster to witch all the pods connect to.

Setup Kube Proxy:
```bash
wget https://storage.googleapis.com/kubernetes-release/release/v1.13.0/bin/linux/amd64/kube-proxy
```
Extract the file
Run kube-proxy as a service:
```bash
ExecStart=/usr/local/bin/kube-proxy\\
    --config=/var/lib/kube-proxy/kube-proxy-config.yaml
Restart=on-failure
RestartSec=5
```

View kube-proxy kubeadm:
```bash
kubectl get pods -n kube-system
kubectl get daemonset -n kube-system
```

## PODS

Pods are the fundamental execution units in Kubernetes, representing a group of one or more containers deployed together on a single node. They share the same network namespace and storage, facilitating communication and data sharing. Pods are the smallest deployable units in the Kubernetes object model.

PODS : smallest object you can create in kubernetes.

For run the python-app: (can run app multiple time)
	docker run python-app
	docker run python-app
	docker run python-app

For run the helper: (will create/die with container --> we should monitor the state of the container)
	docker run helper -link app1
	docker run helper -link app2
	docker run helper -link app3

kubectl:
create a pod automaticly & make instance of nginx ducker image
```bash
kubectl run nginx --image nginx
  ```
list of pods available in our cluster
```bash
kube get pods
```
information about the pod
```bash
kube describe pod myapp-pod 
```
Creat pod with YAML file: 
kubernetes use yaml as inputs to create objects like pods, replica, services,...

example: pod-definition.yml	
you must have these:
	1.apiVersion: v1 [PODS & Service: v1 / ReplicaSet & Deployment: apps/v1]
	2.kind: pod [PODS, Service, ReplicaSet, Deployment]
	3.metadata: [ Its in form of a Dictionary]
	    name: myapp-pod
	    labels:
	        app: myapp
	4.spec:
	    containers: [list/array]
		- name: nginx-container [- : means its the 1st item in the list]
		  image: nginx

    		# you can add more container.
		- name: backend-container
 	     	  image: redis


now create the kubernetes pod:
```bash
kubectl create -f pod-definition.yml
```
when you creat a pod it will create in default namespace

for creating a pod in a different namespace (for example dev namespace):
```bash
kubectl create -f pod-definition.yml --namespace=dev
```
if you want you can move the namespace to the pod-definition.yml under the metadata section.

## REPLICATION CONTROLLER

The Replication Controller ensures a specified number of replicas of a pod are running at all times. It monitors the state of pods and takes corrective actions, such as creating or terminating pods, to maintain the desired number of instances and ensure application availability and resilience.

Replica Sets : 
Replica Sets ensure continuous access to an app and its data, even if a pod stops. with Replica Sets (newer version than replication controller) we unsure that if a pod stops, the user will still have access to the app and data.

Load Balance & Scaling : 
if more users come, we app more pods to we dont run out of resources.if we run out of resources in the first node,  we can add another node.

so we should make a yaml file [we name it ReplicationController-definition.yml]:
in template copy and paste metadata & spec from pod yaml to the template of the rc yaml.

```yaml
apiVersion: v1

kind: ReplicationController

metadata: #[Replication Controller section]
  name: myapp-rc
  labels:
    app: myapp
    type: front-end

spec: #[Replication Controller section]
  template:
    metadata: #[pods section]
      name: myapp-pod
      labels:
        app: myapp
        costcenter: ALPHY
        location: USA

    spec: #[pods section]
      container:
        - name: ngnix-container
          image: ngnix

    # you can add more container.
        - name: backend-container
          image: redis
  replicas: 3 # number of replicas
```


so [Replication Controller]  is the parrent and [the pod] is the children

create and run the replicas:
```bash
kubectl create -f ReplicationController-definition.yml
```
list of replication controller & how many of them are there (number of replicas):
```bash
kubectl get replicationcontroller
```
see pods:
```bash
kubectl get pods
```
somehow replicaset is a process to monitor pods. 
so now we make replicaset-definition.yml file


selector:  # identify what pods fall under it.  replicaset can manage the pods that are not create as the part of the replicaset.y6


for run the replicaset:
```bash
kubectl create -f replicaset-definition.yml
```
see replicaset:
```bash
kubectl get replicaset
```
if we want 6 replicas, in the yaml file change to replica to 6 . and then run the command:
```bash
kubectl replace -f replicaset-definition.yml
```
or use this code instead and use scale for this:
```bash
kubectl scale --replicas=6 -f replicaset-definition.yml
```
or:
				      TYPE	   NAME
	kubectl scale --replicas=6 replicaset myapp-replicaset

## DEPLOYMENTS

Deployments provide declarative updates to applications, allowing rolling updates and rollbacks. They manage Replica Sets, ensuring that the desired state of the application is maintained. Deployments simplify the process of scaling applications and managing their lifecycle.deployments is similar to replicaset but deployments will call a kubernetes objects , named deployment.

DEFINITION:
```bash
kubectl create -f deployment-definition.yml
kubectl get deployments
kubectl get replicaset
kubectl get pods
```

find the image used to create the pod in a new deployment?
```bash
kubectl describe deployments.apps frontend-deployment | grep -i image
```
see how many deployments exist on the system:
```bash
kubectl get deployments.
```
Deploy a pod named nginx-pod using the nginx:alpine image
```bash
kubectl run nginx-pod --image=nginx:alpine --restart=Never
```
Deploy a redis pod  using the redis:alpine image with labels set to tier=db.
```bash
kubectl run redis --image=redis:alpine --restart=Never --labels=tier=db
kubectl get pod redis --show-labels
```
create a deployment named webapp using the image kodekloud/webapp-color with 3 replicas
```bash
kubectl create deployment webapp --image=kodekloud/webapp-color
kubectl scale deployment webapp --replicas=3
```

 ## NAMESPACE

Namespaces provide a way to organize and segregate resources in Kubernetes.Namespaces provide a way to logically divide cluster resources, allowing multiple users or teams to share a cluster without interference. They offer a scope for names and resource isolation, making it easier to organize and manage Kubernetes objects within a cluster.

 Default
kube-system
kube-public


Example:
	Default: web-pod | db-service | web-deployment
	Dev: db-service | web-pod

if we want to connect to internal namespace or outer one:
	mysql.connect("db-service")
 	mysql.connect("db-service.dev.svc.cluster.local")

sevice name:	db-service
Namespace:	dev
Service:	svc
Domain:		cluster.local

```bash
kubectl get pods // only shaw pods from default namespace
kubectl get pods --namespace=kube-system
```

so we make our namespace-dev.yml file:

```yaml
apiVersion: v1
kind: Namespace
metadata:
    name: dev
```

create namespace:
```bash
kubectl create -f namespace-dev.yml
```
or use this one:
```bash
kubectl create namespace dev
```
check:
```bash
kubectl get pods --namespace=dev
```

use the kube config command to set the namespace in the current context:
(contexts are used to manage multiple clusters in multiple envirements from the same management system)
	kubectl config set-context $(kubectl config current-context) --namespace=dev

now you can do this:
```bash
kubectl get pods
kubectl get pods --namespace=default
kubectl get pods --namespace=dev
```

to view pods in all namespaces:
```bash
kubectl get pods --all-namespaces
```
to limit resources in a namespace, create a resource quota.start with creating definition file: compute-quota.yml
in the spec section manage your limit for resources.

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
    name: compute-auota
    namespace: dev

spec:
    hard:
        pods: "10"
        requests.cpu:"4"
        requests.memory: 5 Gi
        limit.cpu:"10"
        limit.memory: 10 Gi
```
now:
	kubectl create -f compute-quota.yml 


 ## SERVICES

Services enable communication between different parts of an application or between applications. They abstract the underlying network details, providing a consistent way to access pods. Services play a crucial role in facilitating connectivity, load balancing, and service discovery within a Kubernetes cluster.helps us connect applications together with other applications or users. 


for  example: application services for backend | frontend | database | ...

setup example:
	kubernetes node : 192.168.1.2
	my laptop in a same network: 192.168.1.10
	internal pod notwork range: 10.244.0.0
	the pod ip : 10.244.0.2

you cant access 10.244.0.2 becouse it is in a seperate network.so if we cant to connect to the web application:

	from 192.168.1.10 --> SSH --> curl http://10.244.0.2

services help us map requests to the node from our laptop, through the node, to the pod, running the web container.

NodePortService:
	listen to port on the node and forward requests on that port, to a port on the pod that running the web application


Services Types:
NodePort Service, Cluster IP, Load Balancer

### Lets dive into these:

### NodePort Service:
listen to port on the node and forward requests on that port, to a port on the pod that running the web application. NodePort Service makese an internal POD accessible on a port on the Node
  
 	TargetPort: were the service forwards the requests to. (In this case : POD IP, for example port is 80 & IP is 10.244.0.2)
 	the cluster IP of service: service port is 80 & the IP is 10.106.1.12
 	Port of the node: in this example 30008

		|	Node 30008 (Range 30000-32767)	| Service 10.106.1.12:80 |	POD 10.244.0.2:80 (TargetPort)	|

Lets make service-definition.yml file. [we know that the pod was created with a label.we have to bring that label into the service definition file (selector part)]
copy and paste from pod-definition.yml file (labels part) to the selector in service-definition.yml file.this link service to the pod.


```yaml
apiVersion: v1
kind: Service
metadata:
 	name: myapp-service

spec:
 	type: NodePort
 	ports:
 	  - targetPort: 80
 		port: 80
 		nodePort: 3008
 	selector:
 		app: my-app
 		type: front-end
```

if you dont provide a target port, its assumed to be the same as port. if you dont provide a  node port, a free port in athe valid range is automatically allocated. 
		
create myapp-service:
```bash
kubectl creat -f server-definition.yml
```
see the created service:
```bash
kubectl get services
```
use the port to access the web service:
```bash
curl http://192.168.1.2:30008
```

what do you do when you have multiple PODs? In a production environment? You have multiple instances of your web application running for high availability and load balancing purposes in this case.
We have multiple similar pods running our web application they all have the same labels with a key app and set to a value of my app the same label is used as a selector during the creation of the service.
So when the service is created it looks for a matching pod with the label and finds three of them.The service then automatically selects all the three pods as endpoints to forward the external requests coming from the user.
it uses a random algorithm does the service acts as a built in load balancer to distribute load across different pods and finally let us look at what happens when the pods are distributed across multiple nodes.

When we create a service without us having to do any additional configuration kubernetes  automatically creates a service that spans across all the nodes in the cluster and maps the target port to the same node port on all the nodes in the cluster.
this way you can access your application using the IP of any node in the cluster and using the same port number which in this case is 30008 .


### Cluster IP:
the service creates a virtual IP inside the cluster to enable communication between different services. such as a set of front end services to set of back end services.
The pods all have an IP address assigned to them as we can see on the screen but these IP as we know are not static.These pods can go down any time and new pods are created all the time.
you cannot rely on these IP addresses for internal communication between the application.A kubernetes service can help us group these PODs together and provide a single interface to access the PODs in a group. 
For example a service created for the backend PODs will help group all the backend PODs together and provide a single interface for other PODs to access this service.

lets create service-definition.yml file:

```yml
apiVersion: v1
kind: Service
metadata:
  name: back-end

spec:
  type: ClusterIP // is the port where the back-end is exposed
  ports:
 	- targetPort: 80
 	port: 80

  selector:
 	app: my-app
 	type: back-end
```
create back-end service:
```bash
kubectl creat -f server-definition.yml
```
see the created service:
```bash
kubectl get services
```
see how many services exist / service type:
```bash
kubectl get svc
```
see targetPort/labels/... configured on the kubernetes service:
```bash
kubectl describe svc kubernetes
```
Lets creat a service: redis-service to expose the redis application whitin the cluster on port 6379
```bash
kubectl expose pod redis --name=redis-service --port=6379 --targe-tport=6379
kubectl describe svc redis-service
```


### Load Balancer:
provisions a load balancer for our service in supported cloud provider.like distribute load across the different web services in your front end tier







