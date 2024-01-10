## Kubernetes

## ETCD

Setup Kubeadm:
kubectl get pods -n kube-system

## KUBE API-SERVER

Kubernetes Architecture:
kubectl get nodes
curl -X POST / api/v1/namespace/default/pods ...[others]

Install Kube API-Server:
wget https://storage.googleapis.com/kubernetes-release/release/v1.13.0/bin/linux/amd64/kube-apiserver

View API-Server Options :
cat /etc/systemd/system/kube-apiserver.service

Running Process / Effective:
ps -aux | grep kube-apiserver

View API-Server Kubeadm:
kubectl get pods -n kube-system

View API-Server Options Kubeadm:
cat /etc/kubernetes/manifests/kube-apiserver.yaml

## SCHEDULER

View scheduler Options :
cat /etc/kubernetes/manifests/kube-scheduler.yaml

scheduler Running Process:
ps -aux | grep kube-scheduler 

## KUBELET

Setup kubelet (allways install kubelet manually in the worker nodes)
wget https://storage.googleapis.com/kubernetes-release/release/v1.13.0/bin/linux/amd64/kubelet

Kubelet Running Process:
ps -aux | grep Kubelet 

## KUBE-PROXY


POD : pod network is an internal virtual network that spans across all the nodes in the cluster to witch all the pods connect to.

Setup Kube Proxy:
wget https://storage.googleapis.com/kubernetes-release/release/v1.13.0/bin/linux/amd64/kube-proxy

Extract the file
Run kube-proxy as a service:
	ExecStart=/usr/local/bin/kube-proxy\\
	    --config=/var/lib/kube-proxy/kube-proxy-config.yaml
	Restart=on-failure
	RestartSec=5

View kube-proxy kubeadm:
kubectl get pods -n kube-system
kubectl get daemonset -n kube-system

## PODS


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
	kubectl run nginx --image nginx --> will create a pod automaticly & make instance of nginx ducker image
	kube get pods --> list of pods available in our cluster
	kube describe pod myapp-pod  --> information about the pod

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
	kubectl create -f pod-definition.yml

when you creat a pod it will create in default namespace

for creating a pod in a different namespace (for example dev namespace):
	kubectl create -f pod-definition.yml --namespace=dev

if you want you can move the namespace to the pod-definition.yml under the metadata section.

## REPLICATION CONTROLLER

Replica Sets : with Replica Sets (newer version than replication controller) we unsure that if a pod stops, the user will still have access to the app and data.

Load Balance & Scaling : if more users come, we app more pods to we dont run out of resources.if we run out of resources in the first node,  we can add another node.


so we should make a yaml file [we name it ReplicationController-definition.yml]:
in template copy and paste metadata & spec from pod yaml to the template of the rc yaml.

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



so [Replication Controller]  is the parrent and [the pod] is the children

create and run the replicas:
	kubectl create -f ReplicationController-definition.yml

list of replication controller & how many of them are there (number of replicas):
	kubectl get replicationcontroller

see pods:
	kubectl get pods

somehow replicaset is a process to monitor pods. 
so now we make replicaset-definition.yml file


selector:  # identify what pods fall under it.  replicaset can manage the pods that are not create as the part of the replicaset.y6


for run the replicaset:
	kubectl create -f replicaset-definition.yml

see replicaset:
	kubectl get replicaset

if we want 6 replicas, in the yaml file change to replica to 6 . and then run the command:
	kubectl replace -f replicaset-definition.yml

or use this code instead and use scale for this:
	kubectl scale --replicas=6 -f replicaset-definition.yml

or:
				      TYPE	   NAME
	kubectl scale --replicas=6 replicaset myapp-replicaset

## DEPLOYMENTS

deployments is similar to replicaset but deployments will call a kubernetes objects call deployment.

DEFINITION:
kubectl create -f deployment-definition.yml
kubectl get deployments
kubectl get replicaset
kubectl get pods


find the image used to create the pod in a new deployment?
	kubectl describe deployments.apps frontend-deployment | grep -i image

 ## NAMESPACE

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


kubectl get pods --> only shaw pods from default namespace
kubectl get pods --namespace=kube-system


so we make our namespace-dev.yml file:

apiVersion: v1
kind: Namespace
metadata:
	name: dev

create namespace:
	kubectl create -f namespace-dev.yml

or use this one:
	kubectl create namespace dev

check:
	kubectl get pods --namespace=dev


use the kube config command to set the namespace in the current context:
(contexts are used to manage multiple clusters in multiple envirements from the same management system)
	kubectl config set-context $(kubectl config current-context) --namespace=dev

now you can do this:
	kubectl get pods
	kubectl get pods --namespace=default
	kubectl get pods --namespace=dev


to view pods in all namespaces:
	kubectl get pods --all-namespaces

to limit resources in a namespace, create a resource quota.start with creating definition file: compute-quota.yml
in the spec section manage your limit for resources.

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

now:
	kubectl create -f compute-quota.yml 

 ## SERVICES

 services(kubenetes object) enable communication between various components within and outside of the application.helps us connect applications together with other applications or users. 


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








