DAY-01
MASTER NODE:

API SERVER: to execute the commands
ETCD: To store the cluster information
SCHEDULER: To schedule the pods
CONTROLLER: To perform operations scheduled by schedulers.

WORKER NODE:

Kubelet: Its an agent which give iformation to master node.
kubeproxy: It deals with networking
pod: group of containers


MINIKUBE: Its a single node cluster.
used to work with k8s on single node.
in real time we use it dev and test env only.
we will not use it on prod.

REQ: 2 cpus, 2 Gb ram, 20Gb ebs, docker, ubuntu 20.04

mkdir k8s
cd k8s
vim minikube.sh

sudo apt update -y
sudo apt upgrade -y
sudo apt install curl wget apt-transport-https -y
sudo curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
sudo curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo mv minikube-linux-amd64 /usr/local/bin/minikube
sudo chmod +x /usr/local/bin/minikube
sudo minikube version
sudo curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
sudo curl -LO "https://dl.k8s.io/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl.sha256"
sudo echo "$(cat kubectl.sha256) kubectl" | sha256sum --check
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
sudo minikube start --driver=docker --force

sh minikube.sh

KUBECTL:
kubectl is the cli tool for k8s.
we use kubectl to execute commands on k8s.


POD:
it is a group of containers.
it is a smallest unit in k8s which we can deploy.
k8s will not work with containers, the works with only pods.
pod -- > cont -- > app 
pod can be created on follwing ways

1. IMPERATIVE - by command

kubectl run pod1 --image=nginx
kubectl get pods/pod/po
kubectl get po -o wide
kubectl describe po pod1
kubectl delete pod pod1

2. DECLARATIVE - by manifest files

vim pod.yml

apiVersion: v1
kind: Pod
metadata:
  name: raham
spec:
  containers:
    - image: nginx
      name: cont1

kubectl create -f pod.yml
kubectl delete -f pod.yml



================================================================================

REPLICASET: it will create multiple copies or replicas of a pod.
if we delete one pod RS will create another pod.

replicaset --- > pods

apiVersion: apps/v1
kind: ReplicaSet
metadata:
  labels:
    app: swiggy
  name: swiggy-rs
spec:
  replicas: 3
  selector:
    matchLabels:
      app: swiggy
  template:
    metadata:
      labels:
        app: swiggy
    spec:
      containers:
      - name: cont1
        image: nginx

kubectl get po 
kubectl get po --show-labels
kubectl get rs/replicaset
kubectl describe rs swiggy-rs
kubectl delete pod swiggy-rs-7nkb2
kubectl get po 
kubectl delete rs swiggy-rs
======================================================================================================================================================


DEPLOYMENT: It is used to update the application.
it will track the changes of an application.

deployment --- > replicaset --- > pods

apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: swiggy
  name: swiggy-deploy
spec:
  replicas: 3
  selector:
    matchLabels:
      app: swiggy
  template:
    metadata:
      labels:
        app: swiggy
    spec:
      containers:
      - name: cont1
        image: nginx:1.16

kubectl get po 
kubectl get po --show-labels
kubectl get rs/replicaset
kubectl describe deploy swiggy-deployment
kubectl edit deployment/swiggy-deployment

SCALING: increasing the pods
kubectl scale deployment/swiggy-deployment --replicas=10
kubectl scale deployment/swiggy-deployment --replicas=20
kubectl scale deployment/swiggy-deployment --replicas=5


history:

 1  mkdir k8s
    2  cd k8s/
    3  vim minikube.sh
    4  sh minikube.sh
    5  minikube status
    6  kubectl get po
    7  kubectl run pod1 --image=nginx
    8  kubectl get po
    9  kubectl get po -o wide
   10  kubectl describe pod pod1
   11  kubectl delete pod pod1
   12  kubectl api-resources
   13  vim raham.yml
   14  kubectl get po
   15  kubectl create -f raham.yml
   16  kubectl get po
   17  kubectl get po --show-labels
   18  kubectl describe pod swiggy-rs-7nkb2
   19  kubectl get replicaset
   20  kubectl get rs
   21  kubectl describe rs swiggy-rs
   22  kubectl get po
   23  kubectl delete pod swiggy-rs-7nkb2
   24  kubectl get po
   25  kubectl delete pod swiggy-rs-hqx6m
   26  kubectl get po
   27  kubectl delete rs swiggy-rs
   28  kubectl get rs
   29  vim raham.yml
   30  kubectl get po
   31  kubectl create -f raham.yml
   32  kubectl get po
   33  kubectl get rs
   34  kubectl get deploy
   35  kubectl describe deploy swiggy-deployment
   36  kubectl edit deployment/swiggy-deployment
   37  kubectl describe deploy swiggy-deployment
   38  kubectl edit deployment/swiggy-deploymentkubectl get po --show-labels
   39  kubectl get po --show-labels
   40  kubectl describe deploy swiggy-deployment
   41  kubectl edit deploymnet/swiggy-deployment
   42  kubectl edit deployment/swiggy-deployment
   43  kubectl describe deploy swiggy-deployment
   44  kubectl scale deployment/swiggy-deployment scale=10
   45  kubectl scale deployment/swiggy-deployment replicas=10
   46  kubectl scale deployment/swiggy-deploymentr --replicas=10
   47  kubectl scale deployment/swiggy-deployment --replicas=10
   48  kubectl get po
   49  kubectl scale deployment/swiggy-deployment --replicas=20
   50  kubectl get po
   51  kubectl scale deployment/swiggy-deployment --replicas=5
   52  kubectl get po
   53  history

=============================

DAY-03:

K8SC
KOPS:

Minikube -- > single node cluster
All the pods on single node 
kOps, also known as Kubernetes operations, is an open-source tool that helps you create, destroy, upgrade, and maintain a highly available, production-grade Kubernetes cluster. Depending on the requirement, kOps can also provide cloud infrastructure.
kOps is mostly used in deploying AWS and GCE Kubernetes clusters. But officially, the tool only supports AWS. Support for other cloud providers (such as DigitalOcean, GCP, and OpenStack) are in the beta stage.

ADVANTAGES:
•	Automates the provisioning of AWS and GCE Kubernetes clusters
•	Deploys highly available Kubernetes masters
•	Supports rolling cluster updates
•	Autocompletion of commands in the command line
•	Generates Terraform and CloudFormation configurations
•	Manages cluster add-ons.
•	Supports state-sync model for dry-runs and automatic idempotency
•	Creates instance groups to support heterogeneous clusters
ALTERNATIVES:
Amazon EKS , MINIKUBE, KUBEADM, RANCHER, TERRAFORM, HELM.

PROCESS:
IAM KEYS: Used to provide access for the kops to create infra.

IAM -- > USERS -- > ADD USERS -- > NAME: kops  -- > NEXT -- > Set permissions: Set permission -- > AdministratorAccess -- > next -- > create
users -- > security credentials -- > create Access keys -- > CLI -- > NEXT -- > CREATE ACCESS KEYS

STEP-1:
ATTACHING KOPS PERMISSIONS TO EC2:
aws configure
cd .aws 
ll
vim config
vim credentials

STEP-2: DOWNLOAD KOPS & KUBECTL 
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
wget https://github.com/kubernetes/kops/releases/download/v1.24.1/kops-linux-amd64
chmod +x kops-linux-amd64 kubectl  # to provide executable permission of both files
mv kubectl /usr/local/bin/kubectl  # all the user executed commands will be on bin folder
mv kops-linux-amd64  /usr/local/bin/kops
vim .bashrc
export PATH=$PATH:/usr/local/bin/
source .bashrc

STEP-3: CREATING BUCKET (To store cluster information) bucket is an object-level store.
aws s3api create-bucket --bucket nareshit0077.k8s.local --region us-east-1
aws s3api put-bucket-versioning --bucket nareshit0077.k8s.local --region us-east-1 --versioning-configuration Status=Enabled
export KOPS_STATE_STORE=s3://nareshit0077.k8s.local

STEP-4: CREATING CLUSTER
kops create cluster --name nareshit0077.k8s.local --zones us-east-1a  --master-size t2.medium --node-size t2.micro
kops update cluster --name nareshit0077.k8s.local --yes --admin


kops delete cluster --name nareshit0077.k8s.local --yes


kops get cluster --full -o yaml
kops create cluster --name rahams.k8s.local --zones us-east-1a --master-count=1 --master-size t2.medium --node-count=2 --node-size t2.micro
kops create cluster --name=rahams.k8s.local --zones=us-east-1a,us-east-1b,us-east-1c --node-count=3 --node-size=t2.medium --node-volume-size=50 


SCRIPT:

vim kops.sh

#! /bin/bash
aws configure
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
wget https://github.com/kubernetes/kops/releases/download/v1.25.0/kops-linux-amd64
chmod +x kops-linux-amd64 kubectl
mv kubectl /usr/local/bin/kubectl
mv kops-linux-amd64 /usr/local/bin/kops

aws s3api create-bucket --bucket nareshitdevops.k8s.local --region us-east-1
aws s3api put-bucket-versioning --bucket nareshitdevops.k8s.local --region us-east-1 --versioning-configuration Status=Enabled
export KOPS_STATE_STORE=s3://nareshitdevops.k8s.local
kops create cluster --name rahams.k8s.local --zones us-east-1a --master-count=1 --master-size t2.medium --node-count=2 --node-size t2.micro
kops update cluster --name rahams.k8s.local --yes --admin

NAMESPACE:

NAME SPACE:

default
kube-node-lease
    This namespace holds Lease objects associated with each node. Node leases allow the kubelet to send heartbeats so that the control plane can detect node failure.
kube-public
    This namespace is readable by all clients (including those not authenticated). 
    This namespace is mostly reserved for cluster usage, in case some resources should be visible and readable publicly throughout the whole cluster. 
    The public aspect of this namespace is only a convention, not a requirement.
kube-system
    The namespace for objects created by the Kubernetes system.


kubectl get namespace 
kubectl create namespace raham
kubectl config set-context --current --namespace=raham

kubectl config view --minify | grep namespace:



HISTORY:

  2  source .bashrc
    3  vim kops.sh
    4  sh kops.sh
    5  vim kops.sh
    6  sh kops.sh
    7  vim kops.sh
    8  sh kops.sh
    9  vim kops.sh
   10  sh kops.sh
   11  vim m
   12  vim kops.sh
   13  sh kops.sh
   14   kops validate cluster --wait 10m
   15  cat kops.sh
   16  export KOPS_STATE_STORE=s3://nareshitdevops.k8s.local
   17   kops validate cluster --wait 10m
   18  kops get cluster
   19  kubectl get no
   20  kubectl get po
   21  kops get po
   22  kubectl get ns
   23  kubectl run pod1 --image=nginx
   24  kubectl get po
   25  kubectl describe pod pod1
   26  kubectl get po
   27  kubectl get po -a
   28  kubectl get po -A
   29  kubectl get po
   30  kubectl get po -A
   31  kubectl get ns
   32  kubectl create ns dev
   33  kubectl get ns
   34  kubectl config set-context --current namepsace=dev
   35  kubectl config set-context --current --namepsace=dev
   36  kubectl config set-context --current --namespace=dev
   37  kubectl config view --minify
   38  kubectl config set-context --current --namespace=default
   39  kubectl config view
   40  kubectl config set-context --current --namespace=dev
   41  kubectl get po
   42  kubectl run pod1 --image=nginx
   43  kubectl run pod2 --image=nginx
   44  kubectl run pod3 --image=nginx
   45  kubectl get po
   46  kubectl config set-context --current --namespace=default
   47  kubectl get po
   48  kubectl create ns
   49  kubectl create ns test
   50  kubectl config set-context --current --namespace=test
   51  kubectl config view
   52  kubectl get po
   53  kubectl get po -n dev
   54  kubectl delete pod pod1 -n dev
   55  kubectl get po -n dev
   56  kubectl describe ns dev
   57  kops delete cluster --name rahams.k8s.local --yes
   58  kops get cluster
   59  cat kops.sh


======================================================

DAY-05:

replication controller:

apiVersion: v1
kind: ReplicationController
metadata:
  name: nginx
spec:
  replicas: 3
  selector:
    app: nginx
  template:
    metadata:
      name: nginx
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx
        ports:
        - containerPort: 80

Deamonset: it will create a single pod on all the servers.

apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: nginx
spec:
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      name: nginx
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx
        ports:
        - containerPort: 80


SERVICES: to expose the application


ClusterIP: A ClusterIP service provides a stable IP address and DNS name for pods within a cluster. 
This type of service is only accessible within the cluster and is not exposed externally.


apiVersion: v1
kind: Pod
metadata:
  name: db
  labels:
    app: phonepay
spec:
  containers:
    - name: cont1
      image: rahamshaik/phonepaymovies:v1
      ports:
      - containerPort: 80

---
apiVersion: v1
kind: Service
metadata:
  name: db
spec:
  type: ClusterIP
  selector:
    app: phonepay
  ports:
    - port: 80
      targetPort: 80


NodePort: A NodePort service provides a way to expose a service on a static port on each node in the cluster. This type of service is accessible both within the cluster and externally, using the node's IP address and the NodePort. (NODEPORT: 30000-32767)

apiVersion: v1
kind: Pod
metadata:
  name: db
  labels:
    app: phonepay
spec:
  containers:
    - name: cont1
      image: rahamshaik/phonepaymovies:v1
      ports:
      - containerPort: 80

---
apiVersion: v1
kind: Service
metadata:
  name: db
spec:
  type: NodePort
  selector:
    app: phonepay
  ports:
    - port: 80
      targetPort: 80
      nodePort: 30001

LOAD BALANCER: A LoadBalancer service provides a way to expose a service externally, using a cloud provider's load balancer. This type of service is typically used when an application needs to handle high-traffic loads and requires automatic scaling and load balancing capabilities.


apiVersion: v1
kind: Pod
metadata:
  name: db
  labels:
    app: phonepay
spec:
  containers:
    - name: cont1
      image: rahamshaik/phonepaymovies:v1
      ports:
      - containerPort: 80

---
apiVersion: v1
kind: Service
metadata:
  name: db
spec:
  type: LoadBalancer
  selector:
    app: phonepay
  ports:
    - port: 80
      targetPort: 80

What is Kubeadm?
Lucas Kaldstrom one of the Kubernetes maintainers for kubeadm talked about some of the internals of Kubeadm and also future plans for its ongoing improvements.
So, what is kubeadm? Kubeadm is a toolkit for bootstrapping and a best-practice for Kubernetes clustering on an existing infrastructure. Kubeadm cannot provision your infrastructure which is one of the main differences to kops. Another differentiator is that Kubeadm can be used not only as an installer but also as a building block.
Kubeadm sets up a minimal viable cluster. It is designed to have all the components you need in one place in one cluster regardless of where you are running them.
An advantage of kubeadm is that it can be used anywhere —even Raspberry Pi— to set up a cluster and try it out before committing to something like kops

Kubespray vs Kops vs Kubeadm
Kubespray runs on bare metal and most clouds, using Ansible as its substrate for provisioning and orchestration. Kubespray is a good choice if you are familiar with Ansible, existing Ansible deployments, or the desire to run a Kubernetes cluster across multiple platforms.
Kops is more tightly integrated with the unique features of the clouds it supports so it could be a better choice if you know that you will only be using one platform for the foreseeable future. Kops performs the provisioning and orchestration itself, and as such is less flexible in deployment platforms
Kubeadm provides domain Knowledge of Kubernetes clusters’ life cycle management, including self-hosted layouts, dynamic discovery services, and so on. Kubespray however, does generic configuration management tasks from the “OS operators” ansible world, plus some initial K8s clustering (with networking plugins included) and control plane bootstrapping


HISTORY:


   24  vim kops.sh
   25  vim .bashrc
   26  source .bashrc
   27  sh kops.sh
   28  vim kops.sh
   29  sh kops.sh
   30  kops validate cluster --wait 10m
   31  cat kops.sh
   32  export KOPS_STATE_STORE=s3://devopsbyraham9988.k8s.local
   33  cat kops.sh
   34  kops validate cluster --wait 10m
   35  kops get cluster
   36  vim rc.yml
   37  kubectl create -f rc.yml
   38  kubectl api-resources
   39  kubectl api-resources | grep -i replication
   40  vim rc.yml
   41  kubectl create -f rc.yml
   42  kubectl api-resources | grep -i replication
   43  rm -rf *
   44  vim repliction.yml
   45  kubectl create -f repliction.yml
   46  kubectl get rc
   47  kubectl get po
   48  kubectl delete po nginx-l4v95
   49  kubectl get po
   50  cat repliction.yml
   51  kubectl get rc
   52  kubectl delete rc nginx
   53  kubectl get rc
   54  vi deamonset.yml
   55  vim repliction.yml
   56  kubectl create -f repliction.yml
   57  vim repliction.yml
   58  kubectl create -f repliction.yml
   59  vim repliction.yml
   60  kubectl create -f repliction.yml
   61  kubectl get po
   62  kubectl get no
   63  cat repliction.yml
   64  vim clusterip.yml
   65  kubectl get svc
   66  kubectl create -f clusterip.yml
   67  kubectl get po
   68  kubectl get svc
   69  kubectl get po --show-labels
   70  kubectl describe db
   71  kubectl describe service db
   72  kubectl describe pod db
   73  kubectl get svc
   74  kubectl delete pod db
   75  kubectl delete svc db
   76  vim clusterip.yml
   77  kubectl apply -f clusterip.yml
   78  kubectl get po,svc
   79  kubectl get po -o wide
   80  kubectl get svc
   81  vim clusterip.yml
   82  kubectl apply -f clusterip.yml
   83  kubectl get svc
   84  vim clusterip.yml
   85  kubectl apply -f clusterip.yml
   86  kubectl get svc
   87  kubectl delete -f clusterip.yml
   88  vim clusterip.yml
   89  kubectl apply -f clusterip.yml
   90  kubectl get po
   91  kubectl get svc
   92  history# k8s-all-cmds
k8s real time useful commands
