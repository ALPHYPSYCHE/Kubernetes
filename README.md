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



