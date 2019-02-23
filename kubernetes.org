* Introduction to Kubernetes
LFS158x

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2018-06-12 20:59:07
[[file:assets/screenshot_2018-06-12_20-59-07.png]]
* Introduction

Kubernetes is an open source system for automating deployment, scaling and management of containerzied applications

It means helmsman, or "ship pilot" in Greek. The analogy is to think of k8s as a manager for ships loaded with containers

K8s has new releases every 3 months. The latest is 1.11

Some of the lessons put in k8s come from Borg, like:
- api servers
- pods
- ip per pod
- services
- labels

K8s has features like:
- automatic binpacking
K8s automatically schedules the containers based on resource usage and constraints

- self healing
Following the declarative paradigm, k8s makes sure that the infra is always what it should be

- horizontal scaling
- service discovery and load balancing
K8s groups sets of containers and refers to them via a DNS. This DNS is also called k8s service. 
K8s can discover these services automatically and load balance requests b/w containers of a given service.

- automated rollouts and rollbacks without downtime
- secrets and configuration management
- storage orchestration
With k8s and its plugins we can automatically mount local, external and storage solutions to the containers in a seamless manner, based on software defined storage (SDS)

- batch execution
K8s supports batch execution

- role based access control

K8s also abstracts away the hardware and the same application can be run on aws, digital ocean, gcp, bare metal, VMs etc once you have the cluster up (and also given you don't use the cloud native solutions like aws ebs etc)

K8s also has a very pluggable architecture, which means we can plug in any of our components and use it. The api can be extended as well. We can write custom plugins too

** Cloud Native Computing Foundation
#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2018-06-12 22:20:29
[[file:assets/screenshot_2018-06-12_22-20-29.png]]

The CNCF is one of the projects hosted by the Linux Foundation. It aims to accelerate the adoption of containers, microservices, cloud native applications.

Some of the projects under the cncf:
- containerd
  - a container runtime - used by docker
- rkt
  - another container runtime from coreos
- k8s
  - container orchestration engine
- linkerd
  - for service mesh
- envoy
  - for service mesh
- gRPC
  - for remote procedure call (RPC)
- container network interface - CNI
  - for networking api
- CoreDNS
  - for service discovery
- Rook
  - for cloud native storage
- notary
  - for security
- The Update Framework - TUF
  - for software updates
- prometheus
  - for monitoring
- opentracing
  - for tracing
- jaeger
  - for distributed tracing
- fluentd
  - for logging
- vitess
  - for storage

this set of CNCF projects can cover the entire lifecycle of an application, from its execution using container runtimes, to its monitoring and logging

The cncf helps k8s by:
- neutral home for k8s trademark and enforces proper usage
- offers legal guidance on patent and copyright issues
- community building, training etc



** K8s architecture

K8s has 3 main components:
- master node
- worker node
- distributed k-v store, like etcd

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2018-06-12 22:30:46
[[file:assets/screenshot_2018-06-12_22-30-46.png]]

The user contacts the ~api-server~ present in the master node via cli, apis, dashboard etc
The master node also has controller, scheduler etc

Each of the worker node has:
- kubelet
- kube-proxy
- pods


*** Master Node
It is responsible for managing the kubernetes cluster. We can have more than 1 master node in our kubernetes cluster. This will enable HA mode. Only one will be master, others will be followers

The distributed k-v store, etcd can be a part of the master node, or it can be configured externally.

**** API server
All the administrative tasks are performed via the api server. The user sends rest commands to the api server which then validates and processes the requests. After executing the requests, the resulting state of the cluster is stored in a distributed k-v store etcd
**** Scheduler
It schedules work on different worker nodes. It has the resource usage information for each worker node. It keeps in mind the constrains that the user might have set on each pod etc. The scheduler takes into account the quality of the service requirements, data locality, affinity, anti-affinity etc

It schedules pods and services

**** Controller manager
It manages non-terminating control loops which regulate the state of the kubernetes cluster. 
The CM knows about the descried state of the objects it manages and makes sure that the object stays in that state. 
In a control loop, it makes sure that the desired state and the current state are in sync

**** etcd
It is used to store the current state of the cluster. 

*** Worker Node

It runs applications using Pods and is controlled by the master node. The master node has the necessary tools to connect and manage the pods. 
A pod is a scheduling unit in kubernetes. It is a logical collection of one or more containers which are always scheduled together.

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2018-06-12 22:47:03
[[file:assets/screenshot_2018-06-12_22-47-03.png]]

A worker node has the following components:
- container runtime
- kubelet
- kube-proxy

**** Continer Runtime
To run and manage the container's lifecycle, we need a container runtime on all the worker nodes. 
Examples include:
- containerd
- rkt
- lxd

**** kubelet

It is an agent that runs on each worker node and communicates with the master node.
It receives the pod definition (for eg from api server, can receive from other sources too) and runs the containers associated with the pod, also making sure that the pods are healthy.

The kublet connects to the container runtime using the CRI - container runtime interface
The CRI consists of protocol buffers, gRPC API, libraries

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2018-06-12 23:27:32
[[file:assets/screenshot_2018-06-12_23-27-32.png]]

The CRI shim converts the CRI commands into commands the container runtime understands

The CRI implements 2 services:

- ImageService
It is responsible for all the image related operations

- RuntimeService
It is responsible for all the pod and container related operations

With the CRI, kubernetes can use different container runtimes. Any container runtime that implements CRI can be used by kubernetes to manage pods, containers, container images

***** CRI shims

Some examples of CRI shims
- dockershim
With dockershim, containers are cerated using docker engine that is installed on the worker nodes. 
The docker engine talks to the containerd and manages the nodes

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2018-06-12 23:44:47
[[file:assets/screenshot_2018-06-12_23-44-47.png]]
***** cri-containerd

With cri-containerd, we directly talk to containerd by passing docker engine

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2018-06-12 23:47:28
[[file:assets/screenshot_2018-06-12_23-47-28.png]]
***** cri-o

There is an initiative called OCI - open container initiative that defines a spec for container runtimes. 
What cri-o does is, it implements the container runtime interface - CRI with a general purpose shim layer that can talk to all the container runtimes that comply with the OCI.

This way, we can use any oci compatible runtime with kubernetes (since cri-o will implement the cri)

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2018-06-12 23:51:33
[[file:assets/screenshot_2018-06-12_23-51-33.png]]

Note here, the cri-o implements the CNI, and also has the image service and the runtime service

***** Notes
It can get a little messy sometimes, all these things. 

Docker engine is the whole thing, it was a monolith that enabled users to run containers. Then it was broken down into individual components. It was broken down into:
- docker engine
- containerd
- runc

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2018-08-11 23:07:13
[[file:assets/screenshot_2018-08-11_23-07-13.png]]

runC is the lowest level component that implements the OCI interface. It interacts with the kernel and does the "runs" the container

containerd does things like take care of setting up the networking, image transfer/storage etc - It takes care of the complete container runtime (which means, it manages and makes life easy for runC, which is the actual container runtime). Unlike the Docker daemon it has a reduced feature set; not supporting image download, for example.

Docker engine just does some high level things itself like accepting user commands, downloading the images from the docker registry etc. It offloads a lot of it to containerd.

"the Docker daemon prepares the image as an Open Container Image (OCI) bundle and makes an API call to containerd to start the OCI bundle. containerd then starts the container using runC."

Note, the runtimes have to be OCI compliant, (like runC is), that is, they have to expose a fixed API to managers like containerd so that they(containerd) can make life easy for them(runC) (and ask them to stop/start containers)

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2018-08-11 23:15:15
[[file:assets/screenshot_2018-08-11_23-15-15.png]]


rkt is another container runtime, which does not support OCI yet, but supports the appc specification. But it is a full fledged solution, it manages and makes it's own life easy, so it needs no containerd like daddy.

So, that's that. Now let's add another component (and another interface) to the mix - Kubernetes

Kubernetes can run anything that satisfies the CRI - container runtime interface. 

You can run rkt with k8s, as rkt satisfies CRI - container runtime interface. Kubernetes doesn't ask for anything else, it just needs CRI, it doesn't give a FF about how you run your containers, OCI or not.

containerd does not support CRI, but cri-containerd which is a shim around containerd does. So, if you want to run containerd with Kubernetes, you have to use cri-containerd (this also is the default runtime for Kubernetes). cri-containerd recently got renamed to CRI Plugin.

If you want to get the docker engine in the mix as well, you can do it. Use dockershim, it will add the CRI shim to the docker engine. 

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2018-08-11 23:27:18
[[file:assets/screenshot_2018-08-11_23-27-18.png]]

Now, like containerd can manage and make life easy for runC (the container runtime), it can manage and make life easy for other container runtimes as well - in fact, for every container runtime that supports OCI - like Kata container runtime (known as ~kata-runtime~ - https://github.com/kata-containers/runtime.) - which runs kata containers, Clear Container runtime (by Intel).

Now we know that rkt satisfies the CRI, cri-containerd (aka CRI Plugin) does it too. 

Note what containerd is doing here. It is not a runtime, it is a manager for runC which is the container runtime. It just manages the image download, storage etc. Heck, it doesn't even satisfy CRI. 

That's why we have CRI-O. It is just like containerd, but it implements CRI. CRI-O needs a container runtime to run images. It will manage and make life easy for that runtime, but it needs a runtime. It will take any runtime that is OCI compliant. So, naturally, ~kata-runtime~ is CRI-O compliant, runC is CRI-O compliant. 

Use with Kubernetes is simple, point Kubernetes to CRI-O as the container runtime. (yes yes, CRI-O, but CRI-O and the actual container runtime IS. And Kubernetes is referring to that happy couple when it says container runtime). 

Like containerd has docker to make it REALLY usable, and to manage and make life easy for containerd, CRI-O needs someone to take care of image management - it has buildah, umochi etc.

crun is another runtime which is OCI compliant and written in C. It is by RedHat.

We already discussed, kata-runtime is another runtime which is OCI compliant. So, we can use kata-runtime with CRI-O like we discussed.

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2018-08-11 23:53:04
[[file:assets/screenshot_2018-08-11_23-53-04.png]]

Note, here, the kubelet is talking to CRI-O via the CRI. CRI-O is talking to cc-runtime (which is another runtime for Intel's clear containers, yes, OCI compliant), but it could be kata-runtime as well.

Don't forget containerd, it can manage and make life easy for all OCI complaint runtimes too - runC sure, but also kata-runtime, cc-runtime

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2018-08-11 23:55:06
[[file:assets/screenshot_2018-08-11_23-55-06.png]]

Here, note just the runtime is moved from runC to kata-runtime. 
To do this, in the containerd config, just change runtime to "kata"

Needless to say, it can run on Kubernetes either by CRI-O, or by cri-containerd (aka CRI Plugin). 


#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2018-08-11 23:56:57
[[file:assets/screenshot_2018-08-11_23-56-57.png]]

This is really cool :top:

Kubernetes, represented here by it's Ambassador, Mr. Kubelet runs anything that satisfies the CRI. 
Now, we have several candidates that can.
- Cri-containerd makes containerd do it.
- CRI-O does it natively.
- Dockershim makes the docker engine do it.

Now, all the 3 guys above, can manage and make life easy for all OCI compliant runtimes - runC, kata-runtime, cc-runtimes.

We also have frakti, which satisfies CRI, like rkt, but doesn't satisfy OCI, and comes bundled with it's own container runtime.


Here we have CRI-O in action managing and making life easy for OCI compliant kata-runtime and runC both

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2018-08-12 00:02:16
[[file:assets/screenshot_2018-08-12_00-02-16.png]]

We have some more runtimes as well:
- railcar - OCI compliant, written in rust
- Pouch - Alibaba's modified runC
- nvidia runtime - nvidia's fork of runC

**** kube-proxy

To connect to the pods, we group them logically, and the use a ~Service~ to connect to them. The service exposes the pods to the external world and load balances across them

Kube-proxy is responsible for setting the routes in the iptables of the node when a new service is created such that the service is accessible from outside. The apiserver gives the service a IP which the kube-proxy puts in the node's iptables

The kube-proxy is responsible for "implementing the service abstraction" - in that it is responsible for exposing a load balanced endpoint that can be reached from inside or outside the cluster to reach the pods that define the service.

Some of the modes in which it operates to achieve that :top:

1. Proxy-mode - userspace

In this scheme, it uses a proxy port.

The kube-proxy does 2 things:
- it opens up a _proxy port_ on each node for each new service that is created
- it sets the iptable rules for each node so that whenever a request is made for the service's ~clusterIP~ and it's port (as specified by the apiserver), the packets come to the _proxy port_ that kube-proxy created. The kube-proxy then uses round robin to forward the packets to one of the pods in that service

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2018-06-13 00:17:51
[[file:assets/screenshot_2018-06-13_00-17-51.png]]

So, let's say the service has 3 pods A, B, C that belong to service S (let's say the apiserver gave it the endpoint 10.0.1.2:44131). Also let's say we have nodes X, Y, Z

earlier, in the userland scheme, each node got a new port opened, say 30333.
Also, each node's iptables got updated with the endpoints of service S (10.0.1.2:44131) pointing to <node A IP>:30333, <node B IP>:30333, <node C IP>:30333

Now, when the request comes to from and node, it goes to <node A IP>:30333 (say) and from there, kube-proxy sends it to the pod A, B or C whichever resides on it. 



2. iptables

Here, there is no central proxy port. For each pod that is there in the service, it updates the iptables of the nodes to point to the backend pod directly.

Continuing the above example, here each node's iptables would get a separate entry for each of the 3 pods A, B, C that are part of the service S.
So the traffic can be routed to them directly without the involvement of kube-proxy

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2018-06-13 00:28:09
[[file:assets/screenshot_2018-06-13_00-28-09.png]]

This is faster since there is no involvement of kube-proxy here, everything can operate in the kernelspace. However, the iptables proxier cannot automatically retry another pod if the one it initially selects does not respond.

So we need a readiness probe to know which pods are healthy and keep the iptables up to date

3. Proxy-mode: ipvs

The kernel implements a virtual server that can proxy requests to real server in a load balanced way. 
This is better since it operates in the kernelspace and also gives us more loadbalancing options


#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2018-06-13 00:32:19
[[file:assets/screenshot_2018-06-13_00-32-19.png]]



**** etcd
Etcd is used for state management. It is the truth store for the present state of the cluster. Since it has very important information, it has to be highly consistent. It uses the raft consensus protocol to cope with machine failures etc.

Raft allows a collection of machines to work as a coherent group that can survive the failures of some of its members. At any given time, one of the nodes in the group will be the master, and the rest of them will be the followers. Any node can be treated as a master.

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2018-06-13 00:35:17
[[file:assets/screenshot_2018-06-13_00-35-17.png]]

In kubernetes, besides storing the cluster state, it is also used to store configuration details such as subnets, ConfigMaps, Secrets etc


**** Network setup challenges
To have a fully functional kubernetes cluster, we need to make sure:
1. a unique ip is assigned to each pod
2. containers in a pod can talk to each other (easy, make them share the same networking namespace )
3. the pod is able to communicate with other pods in the cluster
4. if configured, the pod is accessible from the external world


1. Unique IP
For container networking, there are 2 main specifications:

- Container Network Model - CNM - proposed by docker
- Container Network Interface - CNI - proposed by CoreOS

kubernetes uses CNI to assign the IP address to each Pod

The runtime talks to the CNI, the CNI offloads the task of finding IP for the pod to the network plugin

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2018-06-13 00:52:15
[[file:assets/screenshot_2018-06-13_00-52-15.png]]

2. Containers in a Pod
Simple, make all the containers in a Pod share the same network namespace. This way, they can reach each other via localhost

3. Pod-to-Pod communication across nodes

Kubernetes needs that there shouldn't be any NAT - network address translation when doing pod-to-pod communication. This means, that each pod should have it's own ip address and we shouldn't have say, a subnet level distribution of pods on the nodes (this subent lives on this node, and the pods are accessible via NAT)


4. Communication between external world and pods
This can be achieved by exposing our services to the external world using kube-proxy


** Installing Kubernetes
Kubernetes can be installed in various configurations:
- all-in-one single node installation
Everything on a single node. Good for learning, development and testing. Minikube does this
- single node etcd, single master, multi-worker
- single node etcd, multi master, multi-worker
We have HA
- multi node etcd, multi master, multi-worker
Here, etcd runs outside Kubernetes in a clustered mode. We have HA. This is the recommended mode for production.


Kubernetes on-premise
- Kubernetes can be installed on VMs via Ansible, kubeadm etc
- Kubernetes can also be installed on on-premise bare metal, on top of different operating systems, like RHEL, CoreOS, CentOS, Fedora, Ubuntu, etc. Most of the tools used to install VMs can be used with bare metal as well. 

Kubernetes in the cloud
- hosted solutions
Kubernetes is completely managed by the provider. The user just needs to pay hosting and management charges. 
Examples:
 - GKE
 - AKS
 - EKS
 - openshift dedicated
 - IBM Cloud Container Service

- Turnkey solutions
These allow easy installation of Kubernetes with just a few clicks on underlying IaaS
 - Google compute engine
 - amazon aws
 - tectonic by coreos

- Kubernetes installation tools
There are some tools which make the installation easy
 - kubeadm
This is the recommended way to bootstrap the Kubernetes cluster. It does not support provisioning the machines

 - KubeSpray
It can install HA Kubernetes clusters on AWS, GCE, Azure, OpenStack, bare metal etc. It is based on Ansible and is available for most Linux distributions. It is a Kubernetes incubator project 

 - Kops
Helps us create, destroy, upgrade and maintain production grade HA Kubernetes cluster from the command line. It can provision the machines as well. AWS is officially supported

You can setup Kubernetes manually by following the repo Kubernetes the hard way by Kelsey Hightower.


** Minikube

Prerequisites to run minikube:
- Minikube runs inside a VM on Linux, Mac, Windows. So the use minikube, we need to have the required hypervisor installed first. We can also use ~--vm-driver=none~ to start the Kubernetes single node "cluster" on your local machine
- kubectl - it is a binary used to interact with the Kubernetes cluster


We know about ~cri-o~, which is a general shim layer implementing CRI (container runtime interface) for all OCI (open containers initiative) compliant container runtimes

To use cri-o runtime with minikube, we can do:

~minikube start --container-runtime=cri-o~
then, docker commands won't work. We have to use: ~sudo runc list~ to list the containers for example

** Kubernetes dashboard 

We can use the kubectl cli to access Minikube via CLI, Kubernetes dashboard to access it via cli, or curl with the right credentials to access it via APIs

Kubernetes has an API server, which is the entry point to interact with the Kubernetes cluster - it is used by kubectl, by the gui, and by curl directly as well

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2018-06-13 23:48:48
[[file:assets/screenshot_2018-06-13_23-48-48.png]]


The api space :top: is divided into 3 independent groups. 
- core group
  - ~/api/v1~
  - this includes objects such as pods, services, nodes etc
- named group
  - these include objects in ~/apis/$NAME/$VERSION~ format
    - The different levels imply different levels of stability and support:
      - alpha - it may be dropped anytime without notice, eg: ~/apis/batch/v2alpha1~
      - beta - it is well tested, but the semantics may change in incompatible ways in a subsequent beta or stable release. Eg: ~/apis/certificates.k8s.io/v1beta1~
      - stable - appears in released software for many subsequent versions. Eg ~apis/networking.k8s.io/v1~
- system wide
  - this group consists of system wide API endpoints, like ~/healthz~, ~/logs~, ~/metrics~, ~/ui~ etc


Minikube has a dashboard, start it with ~minikube dashboard~

You can get a dashboard using the ~kubectl proxy~ command also. It starts a service called ~kubernetes-dashboard~ which runs inside the ~kube-system~ namespace
access the dashboard on ~localhost:8001~
once ~kubectl proxy~ is configured, we can use curl to localhost on the proxy port - ~curl http://localhost:8001~

If we don't use ~kubectl proxy~, we have to get a token from the api server by:

~$ TOKEN=$(kubectl describe secret $(kubectl get secrets | grep default | cut -f1 -d ' ') | grep -E '^token' | cut -f2 -d':' | tr -d '\t' | tr -d " ")~

Also, the api server endpoint:
~$ APISERVER=$(kubectl config view | grep https | cut -f 2- -d ":" | tr -d " ")~

Now, it's a matter of a simple curl call:
~$ curl $APISERVER --header "Authorization: Bearer $TOKEN" --insecure~

** Kubernetes building blocks
Kubernetes has several objects like Pods, ReplicaSets, Deployments, Namespaces etc
We also have Labels, Selectors which are used to group objects together.

Kubernetes has a rich object model which is used to represent *persistent entities*
The persistent entities describe:
- what containerized applications we are running, and on which node
- application resource consumption
- different restart/upgrade/fault tolerance policies attached to applications

With each object, we declare our intent (or desired state) using *spec* field.
The Kubernetes api server always accepts only json input. Generally however, we write ~yaml~ files which are converted to json by ~kubectl~ before sending it

Example of deployment object: 
#+begin_src yaml
apiVersion: apps/v1 # the api endpoint we want to connect to
kind: Deployment # the object type
metadata: # as the name implies, some info about deployment object
  name: nginx-deployment
  labels:
    app: nginx
spec: # desired state of the deployment
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec: # desired state of the Pod
      containers:
        - name: nginx
          image: nginx:1.7.9
          ports:
            - containerPort: 80
#+end_src

Once this is created, Kubernetes attaches the ~status~ field to the object

*** Pods

It is the smallest and simplest Kubernetes object, a unit of deployment in Kubernetes.
It is a logical unit representing an application. 
The pod is a logical collection of containers which are deployed on the same host (colocated), share the same network namespace, mount the same external storage volume

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2018-06-14 19:31:56
[[file:assets/screenshot_2018-06-14_19-31-56.png]]

Pods cannot self heal, so we use them with controllers, which can handle pod's replication, fault tolerance, self heal etc.
Examples of controllers:
- Deployments
- ReplicaSets
- ReplicationControllers

We attach the pod's spec (specification) to other objects using pods templates like in previous example

*** Labels

They are key-value pairs that are attached to any Kubernetes objects (like pods)
They are used to organize and select a subset of objects.

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2018-06-14 20:02:04
[[file:assets/screenshot_2018-06-14_20-02-04.png]]


**** Label Selectors

Kubernetes has 2 types of selectors:
- equality based selectors
We can use =, ==, or != operators
- set based selectors
Allows filtering based on a set of values. We can use ~in~, ~notin~, ~exist~ operators. 
Eg: ~env in (dev, qa)~ which allows selecting objects where env label is dev or qa


*** ReplicationControllers

A rc is a controller that is part of the master node's controller manager. It makes sure that the specified number of replicas for a Pod is running - no more, no less.
We generally don't deploy pods on their own since they can't self-heal, we almost always use ~ReplicationController~s to deploy and manage them.

*** ReplicaSets

~rs~ is the next generation ~ReplicationController~. It has both equality and set based selectors. RCs only support equality based controllers. 

RSs can be used independently, but they are mostly used by Deployments to orchestrate pod creation, deletion and updates. 
A deployment automatically creates the ReplicaSets


*** Deployments 
Deployment objects provide declarative (just describe what you want, not how to get it) updates to Pods and ReplicaSets.
#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2018-06-14 20:20:37
[[file:assets/screenshot_2018-06-14_20-20-37.png]]

Here, :top:, the Deployment creates a ~ReplicaSet A~ which creates 3 pods. In each pod, the container runs ~nginx:1.7.9~ image.

Now, we can update the nginx to say ~1.9.1~. This will trigger a new ReplicaSet to be created. Now, this ReplicaSet will make sure that there are the required number of pods as specified in it's spec (that's what it does)

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2018-06-14 20:23:38
[[file:assets/screenshot_2018-06-14_20-23-38.png]]


Once the ReplicaSet B is ready, Deployment starts pointing to it

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2018-06-14 20:24:16
[[file:assets/screenshot_2018-06-14_20-24-16.png]]

The Deployments provide features like Deployment recording, which allows us to rollback if something goes wrong. 

*** Namespaces

If we want to partition our Kubernetes cluster into different projects/teams, we can use Namespaces to logically divide the cluster into sub-clusters.

The names of the resources/objects created inside a namespace are unique, but not across Namespace.

#+begin_src
$ kubectl get namespaces
NAME          STATUS       AGE
default       Active       11h
kube-public   Active       11h
kube-system   Active       11h
#+end_src

The namespace above are:
- default
This is the default namespace
- kube-system
Objects created by the Kubernetes system
- kube-public
It is a special namespace, which is readable by all users and used for special purposes - like bootstrapping a cluster

We can use Resource Quotas to divide the cluster resources within Namespaces. 


** Authentication, Authorization, Admission Control

Each API access request goes thru the following 3 stages:
- authentication
You are who you say you are
- authorization
You are allowed to access this resource 
- admission control
Further modify/reject requests based on some additional checks, like Quota.

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2018-06-14 20:42:57
[[file:assets/screenshot_2018-06-14_20-42-57.png]]

Kubernetes does not have an object called _user_, not does it store _usernames_. 
There are 2 kinds of users:
- normal users
They are managed outside of Kubernetes cluster via independent services like user/client certificates, a file listing usernames/passwords, google accounts etc.

- service accounts
With *Service Account* users, in-cluster processes communicate with the API server. Most of the SA users are created automatically via the API server, or can be created manually. The SA users are tied to a given namespace and mount the respective credentials to communicate with the API server as Secrets. 

*** Authenticator Modules
For authentication, Kubernetes uses different authenticator modules.
- client certificates
We can enable client certificate authentication by giving a CA reference to the api server which will validate the client certificates presented to the API server. The flag is ~--client-ca-file=/path/to/file~

- static token file
We can have pre-defined bearer tokens in a file which can be used with ~--token-auth-file=/path/to/file~
the tokens would last indefinitely, and cannot be changed without restarting the api server

- bootstrap tokens
Can be used for bootstrapping a Kubernetes cluster

- static password file
Similar to static token file. The plag is: ~--basic-auth-file=/path/to/file~. The passwords cannot be changed without restarting the api-server

- service account tokens
This authenticator uses bearer tokens which are attached to pods using the ~ServiceAccount~ admission controller (which allows the in-cluster processes to talk to the api server)

- OpenID Connect tokens
OpenID Connect helps us connect with OAuth 2 providers like Google etc to offload authentication to those services

- Webhook Token Authentication
We can offload verification to a remote service via webhooks

- Keystone password
- Authenticating Proxy
Such as nginx. We have this for our logs stack at Draup


*** Authorization

After authentication, we need authorization. 

Some of the API request attributes that are reviewed by Kubernetes are: user, group, extra, Resource, Namespace etc. They are evaluated against policies. There are several modules that are supported.

- Node Authorizer
It authorizes API requests made by kubelets (it authorizes the kubelet's read operations for services, endpoints, nodes etc, and write operations for nodes, pods, events etc)

- ABAC authorizer - Attribute based access control
Here, Kubernetes grants access to API requests which combine policies with attributes. Eg: 

#+begin_src
{
  "apiVersion": "abac.authorization.kubernetes.io/v1beta1",
  "kind": "Policy",
  "spec": {
    "user": "nkhare",
    "namespace": "lfs158",
    "resource": "pods",
    "readonly": true
  }
}
#+end_src

Here, :top:, ~nkhare~ has only ~read only~ access to pods in namespace ~lfs158~.

To enable this, we have to start the API server with the ~--authorization-mode=ABAC~ option and specify the authorization policy with ~--authorization-policy-file=PolicyFile.json~

- Webhook authorizer
We can offload authorizer decisions to 3rd party services. To use this, start the API server with ~authorization-webhook-config-file=/path/to/file~ where the file has the configuration of the remote authorization service.

- RBAC authorizer - role based access control
Kubernetes has different roles that can be attached to subjects like users, service accounts etc
while creating the roles, we restrict access to specific operations like ~create, get, update, patch~ etc

There are 2 kinds of roles:
- role
With role, we can grant access to 2 kinds of roles:
- Role
We can grant access to resources within a specific namespace

- ClusterRole
Can be used to grant the same permission as Role, but its scope is cluster-wide.

We will only focus on ~Role~

Example:

#+begin_src
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  namespace: lfs158
  name: pod-reader
rules:
- apiGroups: [""] # "" indicates the core API group
  resources: ["pods"]
  verbs: ["get", "watch", "list"]
#+end_src

Here, we created a ~pod-reader~ role which can only access pods in the ~lfs158~ namespace
one we create this Role, we can bind users with ~RoleBinding~

There are 2 kinds of ~RoleBinding~s:
- RoleBinding
This allows us to bind users to the same namespace as a Role. 

- ClusterRoleBinding
It allows us to grant access to resources at cluster-level and to all namespaces


To start API server with rbac option, we use ~--authorization-mode=RBAC~
we can also dynamically configure policies.


*** Admission Control
It is used to specify granular access control policies which include allowing privileged containers, checking on resource quota etc.
There are different admission controllers to enforce these eg: ~ResourceQuota, AlwaysAdmit, DefaultStorageClass~ etc.

They come into affect only after API requests are authenticated and authorized

To use them, we must start the api server with the flag ~admission-control~ which takes a comma separated ordered list of controller names.
~--admission-control=NamespaceLifecycle,ResourceQuota,PodSecurityPolicy,DefaultStorageClass~


** Services

We will learn about services, which are used to group Pods to provide common access points from the external world. 
We will learn about ~kube-proxy~ daemon, which runs each on worker node to provide access to services. 
Also, we'll talk about *service discovery* and *service types* which decide the access scope of a service.

*** Connecting users to Pods

Pods are ephemeral, they can be terminated, rescheduled etc. We cannot connect to them using pod IP directly. Kubernetes provides a higher level abstraction called ~Service~ which logically groups Pods and a policy to access them. 
The grouping is achieved with labels and selectors.

Example consider this:

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2018-06-16 23:33:07
[[file:assets/screenshot_2018-06-16_23-33-07.png]]

Here, we have grouped the pods into 2 logical groups based on the selectors ~frontend~ and ~db~.

We can assign a name to the logical group, called a Service name eg: ~frontend-svc~ and ~db-svc~.

Example:

#+begin_src
kind: Service
apiVersion: v1
metadata:
  name: frontend-svc
spec:
  selector:
    app: frontend
  ports:
    - protocol: TCP
      port: 80
      targetPort: 5000
#+end_src

Here, :top:, we are creating ~frontend-svc~ service. By default each service also gets an IP address which is routable only inside the cluster. 
The IP attached to each service is aka as ~ClusterIP~ for that service (eg: ~172.17.0.4~ here in the diagram)

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2018-06-16 23:36:44
[[file:assets/screenshot_2018-06-16_23-36-44.png]]

The user/client now connects to the IP address which forwards the traffic to the pods attached to it. It does the load balancing, routing etc.

We also in our service spec, defined a ~targetPort~ as 5000. So the service will route the traffic to port 5000 on the pods. If we don't select it, it will be the same port as the service port (80 in the example above)

A tuple of Pods, IP addresses, along with the targetPort is referred to as a Service endpoint. In our case, frontend-svc has 3 endpoints: 10.0.1.3:5000, 10.0.1.4:5000, and 10.0.1.5:5000.



All the worker nodes run ~kube-proxy~ which watches the API server for addition and removal of services. 
For each new service, the ~kube-proxy~ updates the iptables of all the nodes to route the traffic for its ClusterIP to the service endpoints (node-ip:port tuples). It does the load balancing etc. The ~kube-proxy~ _implements_ the service abstraction.

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2018-06-16 23:48:17
[[file:assets/screenshot_2018-06-16_23-48-17.png]]

*** Service Discovery

Services are the primary mode of communication in Kubernetes, so we need a way to discover them at runtime. 
*Kubernetes supports 2 methods of discovering a service*:

- Environment Variables
As soon as a pod runs on any worker node, the ~kubelet~ daemon running on that node adds a set of environment variables in the pod for all the active services. 
Eg: consider a service ~redis-master~, with exposed port ~6379~ and ClusterIP as ~172.17.0.6~ 

This would lead to the following env vars to be declared in the pods:
#+begin_src
REDIS_MASTER_SERVICE_HOST=172.17.0.6
REDIS_MASTER_SERVICE_PORT=6379
REDIS_MASTER_PORT=tcp://172.17.0.6:6379
REDIS_MASTER_PORT_6379_TCP=tcp://172.17.0.6:6379
REDIS_MASTER_PORT_6379_TCP_PROTO=tcp
REDIS_MASTER_PORT_6379_TCP_PORT=6379
REDIS_MASTER_PORT_6379_TCP_ADDR=172.17.0.6
#+end_src

- DNS
Kubernetes has an add-on for DNS, which creates a DNS record for each Service and its format is ~my-svc.my-namespace.svc.cluster.local~. 
Services within the same namespace can reach other service with just their name. For example, if we add a Service redis-master in the my-ns Namespace, then all the Pods in the same Namespace can reach to the redis Service just by using its name, redis-master. Pods from other Namespaces can reach the Service by adding the respective Namespace as a suffix, like redis-master.my-ns. 

This method is recommended. 


*** ServiceType

While defining a Service, we can also choose it's scope. We can decide if the Service
- is accessible only within the cluster
- is accessible from within the cluster AND the external world
- maps to an external entity which resides outside to the cluster

The scope is decided with the ~ServiceType~ declared when creating the service. 

Service types - ClusterIP, NodePort

**** ClusterIP, NodePort

ClusterIP is the default ServiceType. A service gets its virtual IP using the ClusterIP. This IP is used for communicating with the service and is accessible only within the cluster

With the NodePort ServiceType in addition to creating a ClusterIP, a port from the range 30,000-32,767 also gets mapped to the Service from all the worker nodes. 
Eg: if the ~frontend-svc~ has the NodePort ~32233~, then when we connect to any worked node on ~32233~, the packets are routed to the assigned ClusterIP ~172.17.0.4~

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2018-06-17 00:04:39
[[file:assets/screenshot_2018-06-17_00-04-39.png]]

NodePort is useful when we want to make our service accessible to the outside world. The end user connects to the worker nodes on the specified port, which forwards the traffic to the applications running inside the cluster. 

To access the service from the outside world, we need to configure a reverse proxy outside the Kubernetes cluster and map the specific endpoint to the respective port on the worked nodes.

There is another ServiceType: LoadBalancer

**** LoadBalancer
- With this ServiceType, NodePort and ClusterIP services are automatically created, and the external loadbalancer will route to them

- The Services are exposed at a static port on each worker node.
- the Service is exposed externally using the underlying cloud provider's load balancer.

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2018-06-17 00:09:07
[[file:assets/screenshot_2018-06-17_00-09-07.png]]

**** ServiceType: ExternalIP

The cluster administrator can manually configure the service to be mapped to an external IP also. The traffic on the ExternalIP (and the service port) will be routed to one of the service endpoints

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2018-06-17 00:13:05
[[file:assets/screenshot_2018-06-17_00-13-04.png]]

**** ServiceType: ExternalName

It is a special ServiceType that has no selectors, or endpoints. 
When accessed within a cluster, it returns a ~CNAME~ record of an externally configured service.

This is primarily used to make an externally configured service like ~my-db.aws.com~ available inside the cluster using just the name ~my-db~ to other services inside the same namespace. 

*** Deploying a Service

Example of using NodePort

#+begin_src
apiVersion: v1
kind: Service
metadata:
  name: web-service
  labels:
    run: web-service
spec:
  type: NodePort
  ports:
  - port: 80
    protocol: TCP
  selector:
    app: nginx 
#+end_src

Create it using:

#+begin_src
$ kubectl create -f webserver-svc.yaml
service "web-service" created


$ kubectl get svc
NAME          TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
kubernetes    ClusterIP   10.96.0.1      <none>        443/TCP        1d
web-service   NodePort    10.110.47.84   <none>        80:31074/TCP   12s
#+end_src

We can access it at: ~$(CLUSTER_IP):31074~

This is the port that will route the traffic to the service endpoint's port 80
(recall again, the Service Endpoint is just the tuples of (node IP:service port), the service port is the ~targetPort~ in the service spec)

Deploying MongoDB
We need a Deployment and a Service

#+begin_src
apiVersion: apps/v1
kind: Deployment
metadata:
  name: rsvp-db
  labels:
    appdb: rsvpdb
spec:
  replicas: 1
  selector:
    matchLabels:
      appdb: rsvpdb
  template:
    metadata:
      labels:
        appdb: rsvpdb
    spec:
      containers:
      - name: rsvp-db
        image: mongo:3.3
        ports:
        - containerPort: 27017

$ kubectl create -f rsvp-db.yaml
deployment "rsvp-db" created


apiVersion: v1
kind: Service
metadata:
  name: mongodb
  labels:
    app: rsvpdb
spec:
  ports:
  - port: 27017
    protocol: TCP
  selector:
    appdb: rsvpdb


$ kubectl create -f rsvp-db-service.yaml
service "mongodb" created
#+end_src



** Liveness and Readiness Probes

They are used by kubelet to control the health of the application running inside the Pod's container.
Liveness probe is like the health check on AWS' ELB. If the health check fails, the container is restarted

It can be defined as:
- liveness command
- liveness HTTP request
- TCP liveness probe

Example:

#+begin_src
apiVersion: v1
kind: Pod
metadata:
  labels:
    test: liveness
  name: liveness-exec
spec:
  containers:
  - name: liveness
    image: k8s.gcr.io/busybox
    args:
    - /bin/sh
    - -c
    - touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600
    livenessProbe:
      exec:
        command:
        - cat
        - /tmp/healthy
      initialDelaySeconds: 3
      periodSeconds: 5
#+end_src

Here, we start a container with a command which creates a new file in ~/tmp~.
Next, we defined the ~livenessProbe~ to be a command which ~cat~s the file. If it exists, the container is healthy we say.

Deleting this file will trigger a restart

We can also define a HTTP request as the liveness test:

#+begin_src
livenessProbe:
      httpGet:
        path: /healthz
        port: 8080
        httpHeaders:
        - name: X-Custom-Header
          value: Awesome
      initialDelaySeconds: 3
      periodSeconds: 3
#+end_src

Here, we hit the ~/healthz~ endpoint on port ~8080~

We can also do TCP liveness probes
The kubelet attempts to open the TCP socket to the container which is running the application. If it succeeds, the application is considered healthy, otherwise the kubelet marks it as unhealthy and triggers a restart

#+begin_src
livenessProbe:
      tcpSocket:
        port: 8080
      initialDelaySeconds: 15
      periodSeconds: 20
#+end_src

*** Readiness Probe

Sometimes, the pod has to do some task before it can serve traffic. This can be loading a file in memory, downloading some assets etc.
We can use Readiness probes to signal that the container (in the context of Kubernetes, containers and Pods are used interchangeably) is ready to receive traffic.

#+begin_src
readinessProbe:
  exec:
    command:
    - cat
    - /tmp/healthy
  initialDelaySeconds: 5
  periodSeconds: 5
#+end_src

** Kubernetes volume management

Kubernetes uses Volumes for persistent storage. We'll talk about PersistantVolume and PersistentVolumeClaim which help us attach volumes to Pods

A Volume is essentially a directory backed by a storage medium. 

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2018-06-17 11:29:10
[[file:assets/screenshot_2018-06-17_11-29-10.png]]

A Volume is attached to a Pod and shared by the containers of that Pod. 
The volume has the same lifespan as the Pod and it outlives the containers of the Pod - it allows data to be preserved across container restarts.

A directory which is mounted inside a Pod is backed by the underlying Volume Type. The Volume Type decides the properties of the directory, like: size, content etc

There are several volume types:
- emptyDir
An empty Volume is created for the Pod as soon as it is schedules on the worker node. The Volume's life is coupled with the Pod's. When the Pod dies, the contents of the emptyDir Volume are deleted

- hostPath
We can share a directory from the host to the Pod. If the Pod dies, the contents of the hostPath still exist. Their use is not recommended because not all the hosts would have the same directory structure 

- gcePersistentDisk
We can mount Google Compute Engine's PD (persistent disk) into a Pod

- awsElasticBlockStore
We can mount AWS EBS into a Pod

- nfs
We can mount nfs share 

- iscsi
We can mount iSCSI into a Pod. Iscsi stands for (internet small computer systems interface), it is an IP based storage networking standard for linking data storage facilities. 

- secret
With the ~secret~ volume type, we can pass sensitive information such as passwords to pods.

- persistentVolumeClaim

We can attach a PersistentVolume to a pod using PersistentVolumeClaim.
PVC is a volume type

*** PersistentVolumes

In a typical setup, storage is maintained by the system administrators. The developer just gets instructions to use the storage, and doesn't have to worry about provisioning etc

Using vanilla Volume Types makes the same model difficult in the Kubernetes. So we have PersistentVolume (PV), which provides APIs for users and administrators to manage and consume storage of the above Volume Types. 
To manage - PV API resource type
To consume - PVC API resource type

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2018-06-17 11:38:36
[[file:assets/screenshot_2018-06-17_11-38-36.png]]
PV :top:

PVs can be dynamically provisioned as well - using the StorageClass resource. A StorageClass contains pre-defined provisioners and parameters to create a PV. 
How it works is, the user sends a PVC request and this results in the creation of a PV

Some of the Volume Types that support managing storage using PV:

- GCEPersistentDisk
- AWSElasticBlockStore
- AzureFile
- NFS
- iSCSI

*** PersistentVolumeClaim

A PVC is a request for storage by the user. User requests PV resources based on size, access modes etc. 
Once a suitable PV is found, it is bound to a PVC.

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2018-06-17 11:44:02
[[file:assets/screenshot_2018-06-17_11-44-02.png]]

The administrator provisions PVs, the user requests them using PVC. Once the suitable PVs are found, they are bound to the PVC and given to the user to use.

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2018-06-17 11:44:52
[[file:assets/screenshot_2018-06-17_11-44-52.png]]

After use, the PV can be released. The underlying PV can then be reclaimed and used by someone else. 

*** CSI - Container Storage Interface

Note: Kubernetes interfaces are always CXI - Container X Interface (eg: CNI, CSI etc)

We have several CO - Container Orchestraters (Kubernetes, Mesos, Cloud Foundry). Each manages volumes in its own way. This lead to a difficult time for the storage vendors as they have to support all the different COs. 
Also, the code written by the vendors has to live "in-tree" in the COs and has to be tied to the release cycle of the COs. This is not ideal

So, the volume interface is standardized now so that a volume plugin using the CSI would work for all COs.


** ConfigMaps and Secrets

While deploying an application, we may need to pass runtime parameters like endpoints, passwords etc. To do this we can use ~ConfigMap API~ resource. 

We can use ConfigMaps to pass key-value pairs, which can be consumed by pods, or any other system components like controllers.
There are 2 ways to create ConfigMaps:

*** From literal values
Recall literal values are just values defined "in-place"

#+begin_src
$ kubectl create configmap my-config --from-literal=key1=value1 --from-literal=key2=value2
configmap "my-config" created 
#+end_src


*** From files

#+begin_src
apiVersion: v1
kind: ConfigMap
metadata:
  name: customer1
data:
  TEXT1: Customer1_Company
  TEXT2: Welcomes You
  COMPANY: Customer1 Company Technology Pct. Ltd.

$ kubectl create -f customer1-configmap.yaml
configmap "customer1" created
#+end_src

We can use the ConfigMap values from inside the Pod using:

#+begin_src
....
 containers:
      - name: rsvp-app
        image: teamcloudyuga/rsvpapp
        env:
        - name: MONGODB_HOST
          value: mongodb
        - name: TEXT1
          valueFrom:
            configMapKeyRef:
              name: customer1
              key: TEXT1
        - name: TEXT2
          valueFrom:
            configMapKeyRef:
              name: customer1
              key: TEXT2
        - name: COMPANY
          valueFrom:
            configMapKeyRef:
              name: customer1
              key: COMPANY
....
#+end_src

We can also mount a ConfigMap as a Volume inside a Pod. For each key, we will see a file in the mount path and the content of that file becomes the respective key's value.

*** Secrets

Secrets are similar to ConfigMaps in that they are key-value pairs that can be passed on to Pods etc. The only difference being they deal with sensitive information like passwords, tokens, keys etc

The Secret data is stored as plain text inside etcd, so the administrators must restrict access to the api server and etcd

We can create a secret literally
~$ kubectl create secret generic my-password --from-literal=password=mysqlpassword~

The above command would create a secret called my-password, which has the value of the password key set to mysqlpassword.


Analyzing the get and describe examples below, we can see that they do not reveal the content of the Secret. The type is listed as Opaque.
#+begin_src
$ kubectl get secret my-password
NAME          TYPE     DATA   AGE 
my-password   Opaque   1      8m

$ kubectl describe secret my-password
Name:          my-password
Namespace:     default
Labels:        <none>
Annotations:   <none>

Type  Opaque

Data
====
password.txt:  13 bytes
#+end_src

We can also create a secret manually using a YAML configuration file. With secrets, each object data must be encoded using ~base64~.

So:

#+begin_src
# get the base64 encoding of password
$ echo mysqlpassword | base64

bXlzcWxwYXNzd29yZAo=

# now use it to create a secret
apiVersion: v1
kind: Secret
metadata:
  name: my-password
type: Opaque
data:
  password: bXlzcWxwYXNzd29yZAo=
#+end_src


Base64 is not encryption of course, so decrypting is easy:
#+begin_src
$ echo "bXlzcWxwYXNzd29yZAo=" | base64 --decode
#+end_src

Like ConfigMaps, we can use Secrets in Pods using:
- environment variables
#+begin_src
.....
         spec:
      containers:
      - image: wordpress:4.7.3-apache
        name: wordpress
        env:
        - name: WORDPRESS_DB_HOST
          value: wordpress-mysql
        - name: WORDPRESS_DB_PASSWORD
          valueFrom:
            secretKeyRef:
              name: my-password
              key: password
.....
#+end_src

- mounting secrets as a volume inside a Pod. A file would be created for each key mentioned in the Secret whose content would be the respective value.


*** Ingress

We earlier saw how we can access our deployed containerized application from the external world using Services. We talked about ~LoadBalancer~ ServiceType which gives us a load balancer on the underlying cloud platform. This can get expensive if we use too many Load Balancers.

We also talked about NodePort which gives us a port on each worker node and we can have a reverse proxy that would route the requests to the (node-ip:service-port) tuples. 
However, this can get tricky, as we need to keep track of assigned ports etc.

Kubernetes has ~Ingress~ which is another method we can use to access our applications from the external world.

With Services, routing rules are attached to a given Service, they exist as long as the service exists. If we decouple the routing rules from the application, we can then update our application without worrying about its external access.

The Ingress resource helps us do that.

According to kubernetes.io
~an ingress is a collection of rules that allow inbound connections to reach the cluster Services~

To allow inbound connection to reach the cluster Services, ingress configures a L7 HTTP load balancer for Services and provides the following:
- TLS - transport layer security
- Name based virtual hosting
- Path based routing
- Custom rules

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2018-06-17 12:45:17
[[file:assets/screenshot_2018-06-17_12-45-17.png]]

With Ingress, users don't connect directly the a Service. They reach the Ingress endpoint, and from there, the request is forwarded to the respective Service. (note the usage of request, not packets since Ingress is L7 load balancer)

#+begin_src
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: web-ingress
  namespace: default
spec:
  rules:
  - host: blue.example.com
    http:
      paths:
      - backend:
          serviceName: webserver-blue-svc
          servicePort: 80
  - host: green.example.com
    http:
      paths:
      - backend:
          serviceName: webserver-green-svc
          servicePort: 80
#+end_src


The requests for both (blue.example.com and green.example.com) will come to the same Ingress endpoint which will route it to the right Service endpoint

The example above :top: is an example of name based virtual hosting ingress rule

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2018-06-17 13:08:20
[[file:assets/screenshot_2018-06-17_13-08-20.png]]

We can also have fan out ingress rules, in which we send the requests like example.com/blue and example.com/green which would be forwarded to the correct Service

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2018-06-17 13:09:04
[[file:assets/screenshot_2018-06-17_13-09-04.png]]

The Ingress resource uses the Ingress Controller which does the request forwarding.

**** Ingress Controller

It is an application that watches the master node's API server for changes in the ingress resources and updates the L7 load balancer accordingly. 

Kubernetes has several different Ingress Controllers (eg: Nginx Ingress Controller) and you can write yours too.

Once the controller is deployed (recall it's a normal application) we can use it with an ingress resource

#+begin_src
$ kubectl create -f webserver-ingress.yaml
#+end_src

*** Other Kubernetes topics

Kubernetes also has features like auto-scaling, rollbacks, quota management etc


**** Annotations
We can attach arbitrary non-identifying metadata to any object, in a key-value format

#+begin_src
"annotations": {
  "key1" : "value1",
  "key2" : "value2"
}
#+end_src
 
They are not used to identify and select objects, but for:
- storing release ids, git branch info etc
- phone/pages numbers
- pointers to logging etc
- descriptions

Example:

#+begin_src
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: webserver
  annotations:
    description: Deployment based PoC dates 2nd June'2017
....
....
#+end_src

Annotations can be looked at by using describe

~$ kubectl describe deployment webserver~

**** Deployments

If we have recorded our Deployment before doing our update, we can revert back to a know working state if the deployment fails

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2018-06-17 13:55:43
[[file:assets/screenshot_2018-06-17_13-55-43.png]]

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2018-06-17 13:55:28
[[file:assets/screenshot_2018-06-17_13-55-28.png]]


Deployments also has features like:
- autoscaling
- proportional scaling
- pausing and resuming

A deployment automatically creates a ReplicaSet - which makes sure the correct number of Pods are present and pass the liveness probe.

**** Jobs

A Job creates 1 or more Pods to perform a given task. The Job object takes the responsibility of Pod failures and makes sure the task is completed successfully. After the task, the Pods are terminated automatically. 
We can also have cron jobs etc

**** Quota Management
In a multi tenant deployment, fair usage is vital. Administrators can use ResourceQuota object to limit resource consumption per Namespace

We can have the following types of quotas per namespace:
- Compute Resource Quota
We can limit the compute resources (CPU, memory etc) that can be requested in a given namespace

- Storage Resource Quota 
We can limit the storage resources (PVC, requests.storage etc)

- Object Count Quota
We can restrict the number of objects of a given type (Pods, ConfigMaps, PVC, ReplicationControllers, Services, Secrets etc)

This is implemented using cgroups under the hood

**** DaemonSets
If we want a "ghost" Pod(a pod that is running on all nodes at all times), for eg to collect monitoring data from all nodes etc we can use DaemonSet object.

Whenever a node is added to the cluster, a Pod from a given DaemonSet is created on it. If the DaemonSet is deleted, all Pods are deleted as well.

**** StatefulSets
The StatefulSet controller is used for applications that require a unique identity such as name, network identifications, strict ordering etc - eg: mysql cluster, etcd cluster

The StatefulSet controller provides identity and guaranteed ordering of deployment and scaling to Pods.

**** Kubernetes Federation
We can manage multiple Kubernetes clusters from a single control plane using Kubernetes Federation. We can sync resources across the clusters and have cross-cluster discovery, allowing us to do Deployments across regions and access them using a global DNS record.

The Federation is very useful when we want to build a hybrid solution, in which we can have one cluster running inside our private datacenter and another one on the public cloud. We can also assign weights for each cluster in the Federation, to distribute the load as per our choice.

**** Custom Resources
In Kubernetes, a resource is an API endpoint. It stores a collection of API objects. Eg: a Pod resource contains all the Pod objects.

If the existing Kubernetes resources are not sufficient to fulfill our requirements, we can create new resources using *custom resources*

To make a resource declarative(like the rest of Kubernetes), we have to write a custom controller - which can interpret the resource structure and perform the required actions. 

There are 2 ways of adding custom resources:
- CRDs - custom resource definitions 
- API aggregation
They are subordinate API servers which sit behind the primary API server and act as proxy. They offer more fine grained control. 

**** Helm
When we deploy an application on Kubernetes, we have to deal with a lot of manifests (the yaml containing the spec) such as Deployments, Services, Volume Claims, Ingress etc. 
It can be too much work to deploy them one by one specially for the common use cases like deploying a redis cluster etc. 

We can bundle these manifests after templatizing them into a well-defined format (with some metadata). This becomes a package essentially - we call them Charts. 
They can then be served by package managers like Helm. 

Helm is a package manager (analogous to yum and apt) for Kubernetes, which can install/update/delete those Charts in the Kubernetes cluster.

Helm has two components:
- A client called helm, which runs on your user's workstation
- A server called tiller, which runs inside your Kubernetes cluster.


The client helm connects to the server tiller to manage Charts

**** Monitoring and Logging
2 popular solutions are:
- Heapster
It is a cluster wide aggregator of monitoring and event data which is natively supported on Kubernetes.

- Prometheus
It can also be used to scrape resource usage from different Kubernetes components and objects.

We can collect logs from the different components of Kubernetes using fluentd, which is an open source data collector. We can ship the logs to Elasticsearch etc. 


* Kubernetes Up and Running

#+BEGIN_QUOTE
Kubernetes would like to thank every sysadmin who has woken up at 3am to restart a process.
#+END_QUOTE


Kubernetes intends to radically simplify the task of building, deploying, and maintaining distributed systems.


From the first programming languages, to object-oriented programming, to the development of virtualization and cloud infrastructure, the history of computer science is a history of the development of abstractions that hide complexity and empower you to build ever more sophisticated applications.

** Benefits of Kubernetes include:

**** Velocity
The speed with which you can deploy new features and components - while keeping the service up reliably.
Kubernetes provides this by providing immutability, declarative configuration, self healing systems

***** Immutability
Containers and Kubernetes encourage developers to build distributed systems that adhere to the principles of immutable infrastructure. 

With immutable infrastructure, once an artifact is created in the system it does not change via user modifications.

Traditionally, this was not the case, they were treated as mutable infrastructure. With mutable infrastructure, changes are applied as incremental updates to an existing system. 

A system upgrade via the apt-get update tool is
a good example of an update to a mutable system. Running apt sequentially
downloads any updated binaries, copies them on top of older binaries, and makes incremental updates to configuration files.

In contrast, in an immutable system, rather than a series of incremental updates and changes, an entirely new, complete image is built, where the update simply replaces the entire image with the newer image in a single operation. There are no incremental changes. 

In Draup, we have the artifact which is the source code, which is replaced by a new git clone, but the pip packages etc maybe updated/removed incrementally. Hence, we use mutable infrastructure. 
In services, we have docker compose, and subsequently immutable infrastructure. 

Consider containers. What would you rather do?
- You can login to a container,run a command to download your new software, kill the old server, and start the new one.
- You can build a new container image, push it to a container registry,kill the existing container, and start a new one.

In the 2nd case, the entire artifact replacement makes it easy to track changes that you made, and also to rollback your changes. Go-Jek's VP's lecture during the recent #go-include meetup comes to mind, where he spoke about "snowflakes" that are created by mutable infrastructure. 

***** Declarative Configuration

Everything in Kubernetes is a declarative configuration object that represents the desired state of the system. It is Kubernetes’s job to ensure that the actual state of the world matches this desired state.

declarative configuration is an alternative to imperative configuration, where the state of the world is defined by the execution of a series of instructions rather than a declaration of the desired state of the world.

While imperative commands define actions, declarative configurations define state.

To understand these two approaches, consider the task of producing three replicas of a piece of software. With an imperative approach, the configuration would say: “run A, run B, and run C.” The corresponding declarative configuration would be “replicas equals three.”


***** Self healing systems
As a concrete example of the self-healing behavior, if you assert a desired state of three replicas to Kubernetes, it does not just create three replicas — it continuously ensures that there are exactly three replicas. If you manually create a fourth replica Kubernetes will destroy one to bring the number back to three. If you manually destroy a replica, Kubernetes will create one to again return you to the desired state.

**** Scaling (of both software and teams)

Kubernetes achieves and enables scaling by favoring decoupled architectures. 

***** Decoupling
In a decoupled architecture each component is separated from other components by defined APIs and service load balancers.

Decoupling components via load balancers makes it easy to scale the programs that make up your service, because increasing the size (and therefore the capacity) of the program can be done without adjusting or reconfiguring any of the other layers of your service. Each can be scaled independently. 

Decoupling servers via APIs makes it easier to scale the development teams because each team can focus on a single, smaller microservice with a comprehensible surface area

Crisp APIs between microservices (defining an interface b/w services) limit the amount of cross-team communication overhead required to build and deploy software. Hence, teams can be scaled effectively. 

***** Easy scaling
We can have autoscaling at 2 levels
- pods
  - they can be configured to be scaled up or down depending on some predefined condition
  - this assumes that the nodes have resources to support the new number of pods
- cluster nodes
  - since each node is exactly like the previous one, adding a new node to the cluster is trivial and can be done with a few commands or a prebaked image.

Also, since Kubernetes allows us to bin pack, we can place containers from different services onto a single server. This reduces stastical noise and allows us to have a more reliable forecast about growth of different services. 

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2018-08-12 15:12:58
[[file:assets/screenshot_2018-08-12_15-12-58.png]]

Here, we see each time is decoupled by APIs. The Hardware Ops team has to provide the hardware. The kernel team just needs the hardware to make sure their kernel is providing the system calls api. The cluster guys need the api so that they can provision the cluster. The application developers need the kube api to run their apps. Everyone is happy.

**** Abstracting the infrastructure
When your developers build their applications in terms of container images and deploy them in terms of portable Kubernetes APIs, transferring your application between environments, or even running in hybrid environments, is simply a matter of sending the declarative config to a new cluster.

Kubernetes has a number of plug-ins that can abstract you from a particular cloud. For example, Kubernetes services know how to create load balancers on all major public clouds as well as several different private and physical infrastructures. Likewise, Kubernetes PersistentVolumes and PersistentVolumeClaims can be used to abstract your applications away from specific storage implementations.



Container images bundle an application and its dependencies, under a root filesystem, into a single artifact. The most popular container image format is the Docker image format, the primary image format supported by Kubernetes. 

Docker images also include additional metadata used by a container runtime to start a running application instance based on the contents of the container image.

Each layer adds, removes, or modifies files from the preceding layer in the filesystem. This is an example of an overlay filesystem. There are a variety of different concrete implementations of such filesystems, including aufs,
overlay, and overlay2.

*** Dockerfiles

There are several gotchas that come when people begin to experiment with container images that lead to overly large images. The first thing to remember is that files that are removed by subsequent layers in the system are actually still present in the images; they’re just inaccessible.


Another pitfall that people fall into revolves around image caching and building. Remember that each layer is an independent delta from the layer below it. Every time you change a layer, it changes every layer that comes after it. Changing the preceding layers means that they need to be rebuilt, repushed, and repulled to deploy your image to development.


In general, you want to order your layers from least likely to change to most likely to change in order to optimize the image size for pushing and pulling.

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2018-08-12 20:35:27
[[file:assets/screenshot_2018-08-12_20-35-27.png]]

In the 1st case, every time the server.js file changes, the node package layer has to be pushed and pulled. 

Kubernetes relies on the fact that images described in a pod manifest are available across every machine in the cluster. 
So that the scheduler can schedule Pods on any container. 

Recall we heard this point at the Kubernetes meetup organized by the Redhat guys at Go-Jek. 

Docker provides an API for creating application containers on Linux and Windows systems. Note, docker now has windows containers as well. 

It’s important to note that unless you explicitly delete an image it will live on your system forever, even if you build a new image with an identical name. Building this new image simply moves the tag to the new image; it doesn’t delete or replace the old image.
Consequently, as you iterate while you are creating a new image, you will often create many, many different images that end up taking up unnecessary space on your computer.
To see the images currently on your machine, you can use the docker images command. 


** kubectl

kubectl can be used to manage most Kubernetes objects such as pods, ReplicaSets, and services. kubectl can also be used to explore and verify the overall health of the cluster.


*** kubectl describe nodes <node-name>

This gives us information about the instance OS, memory, harddisk space, docker version, running pods etc

#+begin_src
Non-terminated Pods:         (2 in total)
  Namespace                  Name                CPU Requests  CPU Limits  Memory Requests  Memory Limits
  ---------                  ----                ------------  ----------  ---------------  -------------
  kube-system                aws-node-hl4lc      10m (0%)      0 (0%)      0 (0%)           0 (0%)
  kube-system                kube-proxy-bgblb    100m (5%)     0 (0%)      0 (0%)           0 (0%)
#+end_src

Here, note the "requests" and "limits"
Requests are the resources requested by the pod. It is guaranteed to be present. The "limit" is the maximum resources the pod can consume. 

A pod’s limit can be higher than its request, in which case the extra resources are supplied on a best-effort basis. They are not guaranteed to be present on the node.

** Cluster components
Many of the components of Kubernetes are deployed using Kubernetes itself. All of these components run in the kube-system namespace.

*** Kubernetes proxy
It implements the "service" abstraction. It is responsible for routing network traffic to load balanced services in the Kubernetes cluster. 
kube-proxy is implemented in Kubernetes using the ~DaemonSet~ object. 

*** Kubernetes DNS
Kubernetes also runs a DNS server, which provides naming and discovery for the services that are defined in the cluster. This DNS server also runs as a replicated service on the cluster. 

There is also a Kubernetes service that performs load-balancing for the DNS server

#+begin_src
$ k8s kubectl get services --namespace=kube-system kube-dns
NAME       TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)         AGE
kube-dns   ClusterIP   10.100.0.10   <none>        53/UDP,53/TCP   1h
#+end_src

For all containers, the DNS for that pod has been set to point to this internal ip in the ~/etc/resolve.conf~ file for that container.

*** Kubernetes UI

Needs to be deployed.

** Basic ~kubectl~ commands
There are some basic kubectl commands that apply to all Kubernetes objects.

*** Namespaces
Kubernetes uses namespaces to organize objects in the cluster. By default, the ~default~ namespace is used. If you want to use a different namespace, you can pass kubectl the --namespace flag.


*** Contexts
If you want to change the default namespace more permanently, you can use a context.

A context is like a set of settings. It can either have just a different namespace configuration, or can even point to a whole new cluster. 
Note, creating and using contexts gets recorded in the ~$HOME/.kube/config~

Let's create a different namespace context:
~kubectl config set-context my-context --namespace=mystuff~

This creates a new context, but it doesn’t actually start using it yet. To use this
newly created context, you can run:

~$ kubectl config use-context my-context~

*** Viewing Kubernetes resources
Everything contained in Kubernetes is represented by a RESTful resource.

Each Kubernetes object exists at a unique HTTP path; for example, https://your-k8s.com/api/v1/namespaces/default/pods/my-pod leads to the representation of a pod in the default namespace named my-pod. The kubectl command makes HTTP requests to these URLs to access the Kubernetes objects that reside at these paths. By default, it prunes information so that it fits on a single line. To get more info, use ~-o wide~, or ~-o json~, or ~-o yaml~

The most basic command for viewing Kubernetes objects via kubectl is get.

Eg: ~kubectl get <resouce-name>~ will get a listing of all resources in the current namespace. 
To get a particular resource,  ~kubectl get <resouce-name> <object-name>~

kubectl uses the JSONPath query language to select fields in the returned object.
~kubectl get pods my-pod -o jsonpath --template={.status.podIP}~

*** CRUDing Kubernetes objects
Objects in the Kubernetes API are represented as JSON or YAML files. These files are either returned by the server in response to a query or posted to the server as part of an API request.

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2018-08-15 14:49:29
[[file:assets/screenshot_2018-08-15_14-49-29.png]]

To delete, ~kubectl delete -f obj.yaml~

Labels and annotations are tags for your objects.
you can update the labels and annotations on any Kubernetes object using the annotate and label commands. For example, to
add the color=red label to a pod named bar, you can run: ~$ kubectl label pods bar color=red~

*** Debugging commands

To view logs of a container: ~$ kubectl logs <pod-name>~
To execute command on a container ~kubectl exec -it <pod-name> -- bash~
To copy files to and from a container using the cp command ~kubectl cp <pod-name>:/path/to/remote/file /path/to/local/file~



** Pods

Containers in a pod share the host volume. 
#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2018-08-15 15:00:49
[[file:assets/screenshot_2018-08-15_15-00-49.png]]

Here, the web serving and git containers are part of the same logical group, so they are in the same pod. But they are still in separate containers since we don't want one's memory leak to OOM (out of memory, process terminated) the other.

The name goes with the whale theme of Docker containers, since a Pod is also a group of whales.

Each container within a Pod runs in its own cgroup (which means they have their own limits on resource usage), but they share a number of Linux namespaces (eg network)

Applications running in the same Pod share the same IP address and port space (network namespace), have the same hostname (UTS namespace), and can communicate using native interprocess communication channels over System V IPC or POSIX message queues (IPC namespace).

However, applications in different Pods are isolated from each other(since they don't share the namespaces); they have different IP addresses, different hostnames, and more. Containers in different Pods running on the same node might as well be on different servers.


Before putting your containers in same Pod, think:
- do they have a truly symbiotic relationship?
  - as in, can they work if they are on different machines
- do you want to scale them together?
  - as in, it doesn't make sense to scale 1st container without also scaling 2nd container

In general, the right question to ask yourself when designing Pods is, “Will these containers work correctly if they land on different machines?” If the answer is “no,” a Pod is the correct grouping for the containers. If the answer is “yes,” multiple Pods is probably the correct solution. 

In the example of Git pod web server pod, two containers interact via a local filesystem. It would be impossible for them to operate correctly if the containers were scheduled on different machines.

Pods are described in a Pod manifest. The Pod manifest is just a text-file representation of the Pod Kubernetes API object.

Declarative configuration in Kubernetes is the basis for all of the self-healing behaviors in Kubernetes that keep applications running without user action.

The Kubernetes API server accepts and processes Pod manifests before storing them in persistent storage (etcd). 
The scheduler also uses the Kubernetes API to find Pods that haven’t been scheduled to a node. Once scheduled to a node, Pods don’t move and must be explicitly destroyed and rescheduled.

The simplest way to create a Pod is via the imperative ~kubectl run command~.
Eg: ~$ kubectl run kuard --image=gcr.io/kuar-demo/kuard-amd64:1~

#+begin_src yaml
apiVersion: v1
kind: Pod
metadata:
  name: kuard
spec:
  containers:
    - image: gcr.io/kuar-demo/kuard-amd64:1
      name: kuard
      ports:
        - containerPort: 8080
          name: http
          protocol: TCP
#+end_src

This is equivalent to:

The Pod manifest will be submitted to the Kubernetes API server. The Kubernetes system will then schedule that Pod to run on a healthy node in the cluster, where it will be monitored by the kubelet daemon process. 

#+begin_src
$ docker run -d --name kuard \ --publish 8080:8080 gcr.io/kuar-demo/kuard-amd64:1
#+end_src

Get running pods: ~$ kubectl get pods~

Get more info: ~kubectl describe pods kuard~
Deleting a pod: ~kubectl delete pods/kuard~ or via the file ~kubectl delete -f kuard-pod.yaml~

When a Pod is deleted, it is not immediately killed. Instead, if you run kubectl get pods you will see that the Pod is in the Terminating state. All Pods have a
termination grace period. By default, this is 30 seconds. When a Pod is transitioned to Terminating it no longer receives new requests. In a serving
scenario, the grace period is important for reliability because it allows the Pod to finish any active requests that it may be in the middle of processing before it is terminated.
It’s important to note that when you delete a Pod, any data stored in the containers associated with that Pod will be deleted as well. If you want to persist data across multiple instances of a Pod, you need to use PersistentVolumes.

The data is not deleted when the pod is restarted etc

*you can port forward directly to localhost using kubectl*
kubectl port-forward kuard 8080:8080

On running this :top:, a secure tunnel is created from your local machine, through the Kubernetes master, to the instance of the Pod running on one of the worker nodes.


You can run commands on the pod too:
~$ kubectl exec kuard date~, even an interactive one ~$ kubectl exec -it kuard bash~

Copying files to and fro is easy:
~$ kubectl cp <pod-name>:/captures/capture3.txt ./capture3.txt~

Generally speaking, copying files into a container is an antipattern. You really should treat the contents of a container as immutable.

When you run your application as a container in Kubernetes, it is automatically kept alive for you using a process health check. This health check simply ensures that the main process of your application is always running. If it isn’t, Kubernetes restarts it.

However, in most cases, a simple process check is insufficient. For example, if your process has deadlocked and is unable to serve requests, a process health check will still believe that your application is healthy since its process is still running.
To address this, Kubernetes introduced health checks for application liveness. Liveness health checks run application-specific logic (e.g., loading a web page) to verify that the application is not just still running, but is functioning properly. Since these liveness health checks are application-specific, you have to define them in your Pod manifest.

Liveness probes are defined per container, which means each container inside a Pod is health-checked separately.

#+begin_src
apiVersion: v1
kind: Pod
metadata:
  name: kuard
spec:
  containers:
    - image: gcr.io/kuar-demo/kuard-amd64:1
      name: kuard
      ports:
        - containerPort: 8080
          name: http
          protocol: TCP
      livenessProbe:
        httpGet:
          path: /healthy
          port: 8080
        initialDelaySeconds: 5
        timeoutSeconds: 1
        periodSeconds: 10
        failureThreshold: 3
#+end_src

If the probe fails, the pod is restarted.
Details of the restart can be found with kubectl describe kuard. The “Events” section will have text similar to the
following:
   Killing container with id docker://2ac946...:pod "kuard_default(9ee84...)"
   container "kuard" is unhealthy, it will be killed and re-created.


Kubernetes makes a distinction between liveness and readiness. Liveness determines if an application is running properly. Containers that fail liveness checks are restarted. Readiness describes when a container is ready to serve user requests. Containers that fail readiness checks are removed from service load balancers.
Readiness probes are configured similarly to liveness probes.

*** Health checks
Kubernetes supports different kinds of healthchecks:
- tcpSocket
  - it the tcp connection succeeds, considered healthy. This is for non http applications, like databases etc
- exec
  - These execute a script or program in the context of the container. Following typical convention, if this script returns a zero exit code, the probe succeeds; otherwise, it fails. exec scripts are often useful for custom application validation logic that doesn’t fit neatly into an HTTP call.

Kubernetes allows users to specify two different resource metrics. Resource requests specify the minimum amount of a resource required to run the application. Resource limits specify the maximum amount of a resource that an application can consume.

#+begin_src
apiVersion: v1
kind: Pod
metadata:
  name: kuard
spec:
  containers:
    - image: gcr.io/kuar-demo/kuard-amd64:1
 name: kuard
resources:
  requests:
    cpu: "500m"
    memory: "128Mi"
ports:
  - containerPort: 8080
    name: http
    protocol: TCP
#+end_src

Here, :top:, we requested a machine with at least half cpu free, and 128mb of memory. 

a Pod is guaranteed to have at least the requested resources when running on the node. Importantly, “request” specifies a minimum. It does not specify a maximum cap on the resources a Pod may use.

Imagine that we have container whose code attempts to use all available CPU cores. Suppose that we create a Pod with this container that requests 0.5 CPU. 

Kubernetes schedules this Pod onto a machine with a total of 2 CPU cores.
As long as it is the only Pod on the machine, it will consume all 2.0 of the available cores, despite only requesting 0.5 CPU.

If a second Pod with the same container and the same request of 0.5 CPU lands on the machine, then each Pod will receive 1.0 cores.

If a third identical Pod is scheduled, each Pod will receive 0.66 cores. Finally, if a fourth identical Pod is scheduled, each Pod will receive the 0.5 core it requested, and the node will be at capacity.

CPU requests are implemented using the cpu-shares functionality in the Linux kernel.

With memory, it is a little different. If we put a pod with say 256MB requested and it takes up everything, on adding a new pod, we can't take away half the memory, since it is being used. Here, the pod is killed and restarted, but with less available memory on the machine for the container to consume.

To cap the max a pod can use, we can set the Limits

#+begin_src
apiVersion: v1
kind: Pod
metadata:
  name: kuard
spec:
  containers:
    - image: gcr.io/kuar-demo/kuard-amd64:1
      name: kuard
      resources:
        requests:
          cpu: "500m"
          memory: "128Mi"
        limits:
          cpu: "1000m"
          memory: "256Mi"
      ports:
        - containerPort: 8080
          name: http
          protocol: TCP
#+end_src

A container with a CPU limit of 0.5 cores will only ever get 0.5 cores, even if the CPU is otherwise idle.

*** Persisting Data with Volumes

When a Pod is deleted or a container restarts, any and all data in the container’s filesystem is also deleted.

To persist data beyond the pod, use Volumes.

There are 2 additions to add volumes to pods:
- spec.volumes
  - This array defines all of the volumes that may be accessed by containers in the Pod manifest. Note that not all containers are required to mount all volumes defined in the Pod.
- volumeMounts
  - This array  defines the volumes that are mounted into a particular container, and the path where each volume should be mounted. Note that two different containers in a Pod can mount the same volume at different mount paths.


So, first in spec.volumes, we define what volumes may be used by the containers in the Pod. And, in volumeMounts, we actually use them.

#+begin_src
apiVersion: v1
kind: Pod
metadata:
  name: kuard
spec:
  volumes:
    - name: "kuard-data"
      hostPath:
        path: "/var/lib/kuard"
  containers:
    - image: gcr.io/kuar-demo/kuard-amd64:1
      name: kuard
      volumeMounts:
        - mountPath: "/data"
          name: "kuard-data"
      ports:
        - containerPort: 8080
          name: http
          protocol: TCP
#+end_src

Here, we define kuard-data as the volume, and then mount it on the kuard container. 

There are various types of volumes:
- ~emptyDir~
  - Such a volume is scoped to the Pod’s lifespan, but it can be shared between two containers. (in our example above, this forms the basis for communication between our Git sync and web serving containers). This survives the pod restart
- ~hostDir~
  - this can mount arbitrary locations on the worker node into the container
  - this was used in the example above :top:
  - This can be used when the pod wants to direct access to the instance's block storage for eg. But shouldn't be used to store ordinary data since not all the hosts would have the same underlying dir structure.
- network storage
  - if you want the data to stay with the Pod even when the pod is moved around, restarted etc, use one of the several options available in the network based storage
  - Kubernetes includes support for standard protocols such as NFS and iSCSI as well as cloud provider–based storage APIs for the major cloud providers (both public and private)

#+begin_src
# Rest of pod definition above here
   volumes:
       - name: "kuard-data"
         nfs:
           server: my.nfs.server.local
           path: "/exports"
#+end_src


Once you’ve submitted the manifest to the API server, the Kubernetes scheduler finds a machine where the Pod can fit and schedules the Pod to that machine(note, it first finds the node to host the Pod). Once scheduled, the kubelet daemon on that machine is responsible for creating
the containers that correspond to the Pod, as well as performing any health checks defined in the Pod manifested.

We can use an ReplicaSet object to automate the creation of multiple identical Pods and ensure that they are recreated in the event of a node machine failure.

*** Labels and Annotations

Labels and annotations let you work in sets of things that map to how you think about your application. You can organize, mark, and cross-index all of your resources to represent the groups that make the most sense for your application.

Labels are key/value pairs that can be attached to Kubernetes objects such as Pods and ReplicaSets. Both the key and value are represented by strings. Names must also start and end with an alphanumeric character and permit the use of dashes (-), underscores (_), and dots (.) between characters.

Annotations are key/value pairs designed to hold nonidentifying information that can be leveraged by tools and libraries.

Labels are for your use, annotations are for use by tools (including Kubernetes) and libraries

You can apply a label like so: ~kubectl label deployments alpaca-test "canary=true".~ and remove it like so: ~$ kubectl label deployments alpaca-test "canary-".~

Label selectors are used to filter Kubernetes objects based on a set of labels. Selectors use a simple Boolean language. They are used both by end users (via tools like kubectl) and by different types of objects (such as how ReplicaSet
relates to its Pods).

Eg: ~$ kubectl get pods --selector="ver=2" .~
supports AND - ~$ kubectl get pods --selector="app=bandicoot,ver=2".~
supports OR - ~$ kubectl get pods --selector="app in (alpaca,bandicoot)".~

Each deployment (via a ReplicaSet) creates a set of Pods using the labels specified in the template embedded in the deployment. 


When a Kubernetes object refers to a set of other Kubernetes objects, a label selector is used.

#+begin_src
selector:
     matchLabels:
       app: alpaca
     matchExpressions:
       - {key: ver, operator: In, values: [1, 2]}
#+end_src

Annotations provide a place to store additional metadata for Kubernetes objects with the sole purpose of assisting tools and libraries. They can be used for the tool itself or to pass configuration information between external systems.

There is overlap, and it is a matter of taste as to when to use an annotation or a label. When in doubt, add information to an object as an annotation and promote it to a label if you find yourself wanting to use it in a selector.

Annotations are used by Kubernetes too:
- Communicate a specialized scheduling policy to a specialized scheduler.
- Enable the Deployment object to keep track of ReplicaSets that it is managing for rollouts.
- Prototype alpha functionality in Kubernetes (instead of creating a first-class API field, the parameters for that functionality are instead encoded in an annotation).
- During rolling deployments, annotations are used to track rollout status and provide the necessary information required to roll back a deployment to a previous state.

The value component of an annotation is a free-form string field.

Annotations are defined in the common metadata section in every Kubernetes object:

#+begin_src
   metadata:
     annotations:
       example.com/icon-url: "https://example.com/icon.png"
#+end_src

Using labels and annotations properly unlocks the true power of Kubernetes’s flexibility and provides the starting point for building automation tools and deployment workflows.

*** Service Discovery
Service discovery tools help solve the problem of finding which processes are listening at which addresses for which services.

A good service discovery tool has these features:
- low latency to requests
- is able to store richer information - like ports the services are running on
- information propagates quickly

Real service discovery in Kubernetes starts with a Service object.

Just as the kubectl run command is an easy way to create a Kubernetes deployment(and start pods), we can use kubectl expose to create a service.

By default, we have the kubernetes service already created for us so that we can find and talk to the Kubernetes API

The service is assigned a new type of virtual IP called a cluster IP. This is a special IP address the system will load-balance across all of the pods that are identified by the selector.

Because the cluster IP is virtual it is stable and it is appropriate to give it a DNS address. 

Kubernetes provides a DNS service exposed to Pods running in the cluster. This Kubernetes DNS service was installed as a system component when the cluster was first created. The DNS service is, itself, managed by Kubernetes and is a great example of Kubernetes building on Kubernetes. The Kubernetes DNS service provides DNS names for cluster IPs.

When we expose a service ~myservice~, it is available in the cluster as:
~myservice.default.svc.cluster.local~.
The syntax is: ~service-name.namespace.svc.cluster.local~
- ~svc~ is required to allow Kubernetes to expose other types of things as DNS in the future
- ~cluster.local~ can be changed if required to allow unique DNS names across multiple clusters.

When referring to the service from same namespace, you can use ~myservice~, else, ~myservice.default~ works. Full name works as well.

To allow outside traffic to come in, we need ~NodePorts~.
In addition to a cluster IP, the system picks a port (or the user can specify one), and every node in the cluster then forwards traffic to that port to the service.

Finally, if you have support from the cloud that you are running on you can use the LoadBalancer type.
This builds on NodePorts by additionally configuring the cloud to create a new load balancer and direct it at nodes in your cluster.

Under the hood, with each service, Kubernetes creates an object called ~Endpoints~ that contains the IP address for that service.

To use a service, an advanced application can talk to the Kubernetes API directly to look up endpoints and call them.

Cluster IPs are stable virtual IPs that load-balance traffic across all of the endpoints in a service. This is performed by a component running on every node in the cluster called the kube-proxy

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2018-08-15 22:42:36
[[file:assets/screenshot_2018-08-15_22-42-36.png]]

The ~kube-proxy~ looks for new services in the cluster from the apiserver. When a new service comes in, it writes the iptables rules on the node, so that packets can be routed. If the set of endpoints for a service changes (due to pods coming and going or due to a failed readiness check) the set of iptables rules is rewritten.

Once set, the cluster IP cannot be modified without deleting and recreating the Service object.

The Kubernetes service address range is configured using the --service-cluster-ip-range flag on the kube-apiserver binary.

EKS does not support modifying all the flags on the apiserver.

Earlier, for SD, pods used to define env vars to know the ClusterIP of the service they wanted to access. A problem with the environment variable approach is that it requires resources to be created in a specific order. 
The services must be created before the pods that reference them. This can introduce quite a bit of complexity when deploying a set of services that make up a larger application. 


*** ReplicaSets

Generally, we want multiple pods to run, not a single instance. This can be for:
- Redundancy (so we can survive failure of a single pod)
- Scale
- Sharding (Different replicas can handle different parts of a computation in parallel.)

A user managing a replicated set of Pods considers them as a single entity to be defined and managed. This is precisely what a ReplicaSet is. 

A ReplicaSet acts as a cluster-wide Pod manager, ensuring that the right types and number of Pods are running at all times.

ReplicaSets are the building blocks for self healing infrastructure. 
Pods managed by ReplicaSets are automatically rescheduled under certain failure conditions such as node failures and network partitions.


The actual act of managing the replicated Pods is an example of a reconciliation loop. Such loops are fundamental to most of the design and implementation of Kubernetes. They are part of the controller binary. 

Kubernetes is completely decoupled. All of the core concepts of Kubernetes are modular with respect to each other and that they are swappable and replaceable with other components.

Though ReplicaSets create and manage Pods, they do not own the Pods they create. ReplicaSets use label queries to identify the set of Pods they should be managing. This makes ReplicaSets very clean and decoupled. Their code can be used to manage something else also, based on their labels.

Another example of decoupling, ReplicaSets that create multiple Pods and the services that load-balance to those Pods are also totally separate, decoupled API objects. 

This has several advantages:

- Adopting Existing Containers
  - And because ReplicaSets are decoupled from the Pods they manage, you can simply create a ReplicaSet that will “adopt” the existing Pod, and scale out additional copies of those containers.
- Quarantining Containers
  - If a pod is misbehaving, you can change the label on it and ReplicaSet will see that one pod is less and create a new one. Now, you can debug the pod.



ReplicaSets are designed to represent a single, scalable microservice inside your architecture. Every Pod that is created by the ReplicaSet controller is entirely homogeneous. 
Typically, these Pods are then fronted by a Kubernetes service load balancer, which spreads traffic across the Pods that make up the service. Generally speaking, ReplicaSets are designed for stateles (or nearly stateless) services.


**** ReplicaSet spec

All ReplicaSets must have a unique name (~metadata.name~), the number of Pods (replicas) that should be running cluster-wide at a given time, and a Pod template that describes the Pod to be created when the defined number of replicas is not met.

#+begin_src
apiVersion: extensions/v1beta1
kind: ReplicaSet
metadata:
  name: kuard
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: kuard
        version: "2"
    spec:
      containers:
        - name: kuard
          image: "gcr.io/kuar-demo/kuard-amd64:2"
#+end_src

Note, the ~spec.template.metadata.labels~, these are the labels that are monitored by the rs

The selector in the ReplicaSet spec should be a proper subset of the labels in the Pod template.
 
Sometimes you may wonder if a Pod is being managed by a ReplicaSet, and, if it is, which ReplicaSet. To enable this kind of discovery, the ReplicaSet controller adds an annotation to every Pod that it creates. The key for the annotation is ~kubernetes.io/created-by~.

Note that such annotations are best-effort; they are only created when the Pod is created by the ReplicaSet, and can be removed by a Kubernetes user at any time.


**** Autoscaling
Autoscaling means nothing, there are multiple types of autoscaling:
- horizontal autoscaling
  - increasing the number of pods in response to load
- vertical autoscaling
  - increasing the resources for each running pod

To autoscale on CPU or some other metric, you need ~heapster~ installed. 
~kubectl autoscale rs kuard --min=2 --max=5 --cpu-percent=80~

This command creates an autoscaler that scales between two and five replicas with a CPU threshold of 80%.

To view, modify, or delete this resource you can use the standard kubectl commands and the horizontalpodautoscalers
resource. horizontalpodautoscalers is quite a bit to type, but it can be shortened to hpa: ~$ kubectl get hpa~

It’s a bad idea to combine both autoscaling and imperative or declarative management of the number of replicas, so use either static number of pods, or use hpa

If you don’t want to delete the Pods that are being managed by the ReplicaSet
you can set the --cascade flag to false to ensure only the ReplicaSet object is deleted and not the Pods:
~$ kubectl delete rs kuard --cascade=false~



*** DaemonSets

This is used if you want to schedule a single Pod on every node in the cluster. 
DaemonSets are used to deploy system daemons such as log collectors and monitoring agents, which typically must run on every node.
Generally, these pods are expected to be long running services.

You can exclude any node in DaemonSets if you wish by specifying the ~nodeName~ field in the Pod spec. 

#+begin_src yaml
apiVersion: extensions/v1beta1
kind: DaemonSet
metadata:
  name: fluentd
  namespace: kube-system
  labels:
    app: fluentd
spec:
  template:
    metadata:
      labels:
        app: fluentd
    spec:
      containers:
      - name: fluentd
        image: fluent/fluentd:v0.14.10
        resources:
          limits:
            memory: 200Mi
          requests:
            cpu: 100m
            memory: 200Mi
        volumeMounts:
        - name: varlog
          mountPath: /var/log
        - name: varlibdockercontainers
          mountPath: /var/lib/docker/containers
          readOnly: true
      terminationGracePeriodSeconds: 30
      volumes:
      - name: varlog
        hostPath:
          path: /var/log
      - name: varlibdockercontainers
        hostPath:
          path: /var/lib/docker/containers
#+end_src

Each DaemonSet must include a Pod template spec, which will be used to create Pods as needed. 

You can use labels to select nodes on which to run the DaemonSet

The following DaemonSet configuration limits nginx to running only on nodes with the ssd=true label set
#+begin_src yaml
apiVersion: extensions/v1beta1
kind: "DaemonSet"
metadata:
  labels:
    app: nginx
    ssd: "true"
  name: nginx-fast-storage
spec:
  template:
    metadata:
      labels:
app: nginx
        ssd: "true"
    spec:
      nodeSelector:
        ssd: "true"
      containers:
        - name: nginx
          image: nginx:1.10.0
#+end_src

Of course, set the labels using: ~kubectl label nodes k0-default-pool-35609c18-z7tb ssd=true~

Adding the ssd=true label to additional nodes will case the nginx-fast- storage Pod to be deployed on those nodes. The inverse is also true: if a required label is removed from a node, the Pod will be removed by the DaemonSet controller.

This is the reconciliatory loop in action. 

Prior to Kubernetes 1.6, the only way for pods in DaemonSets to be updated was to delete them.   


Now, you can do rolling updates using ~spec.updateStrategy.type~ -> ~RollingUpdate~ etc

There are 2 parameters: 
- ~spec.minReadySeconds~ (how long a Pod must be “ready” before the rolling update proceeds to upgrade subsequent Pods)
- ~spec.updateStrategy.rollingUpdate.maxUnavailable~ (how many Pods may be simultaneously updated by the rolling update)



DaemonSets provide an easy-to-use abstraction for running a set of Pods on every node in a Kubernetes cluster, or if the case requires it, on a subset of nodes based on labels. The DaemonSet provides its own controller and scheduler.

These DaemonSets aren’t really traditional serving applications, but rather add additional capabilities and features to the Kubernetes cluster itself.

*** Jobs

They are short-lived, one-off tasks which run on Kubernetes.

A Job creates Pods that run until successful termination (i.e., exit with 0). In contrast, a regular Pod will continually restart regardless of its exit code.

They are represented by the ~Job~ object. The Job object coordinates running a number of pods in parallel.


If the Pod fails before a successful termination, the Job controller will create a new Pod based on the Pod template in the Job specification. 

The 2 parameters that are important are: ~completions~, and ~parallelism~


#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2018-08-16 21:44:19
[[file:assets/screenshot_2018-08-16_21-44-19.png]]

After the Job has completed, the Job object and related Pod are still around. This is so that you can inspect the log output. Note that this Job won’t show up in kubectl get jobs unless you pass the -a flag. Without this flag kubectl hides completed Jobs.


#+begin_src yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: oneshot
  labels:
    chapter: jobs
spec:
  template:
    metadata:
      labels:
        chapter: jobs
    spec:
      containers:
      - name: kuard
        image: gcr.io/kuar-demo/kuard-amd64:1
        imagePullPolicy: Always
        args:
        - "--keygen-enable"
        - "--keygen-exit-on-complete"
        - "--keygen-num-to-gen=10"
      restartPolicy: OnFailure
#+end_src



Generating keys can be slow. Let’s start a bunch of workers together to make key generation faster. We’re going to use a combination of the completions and
parallelism parameters. Our goal is to generate 100 keys by having 10 runs of kuard with each run generating 10 keys. But we don’t want to swamp our cluster, so we’ll limit ourselves to only five pods at a time.
This translates to setting completions to 10 and parallelism to 5.

The spec now has: ~spec.parallelism=5~ and ~spec.completions=10~

**** Work Queues
There is a producer that puts jobs on the Work queue, and there are multiple consumers that consume the tasks.

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2018-08-17 13:44:49
[[file:assets/screenshot_2018-08-17_13-44-49.png]]
The Job abstraction allows you to model batch job patterns ranging from simple one-time tasks to parallel jobs that process many items until work has been exhausted.

Jobs are a low-level primitive and can be used directly for simple workloads. Or you can make libraries to build on top of them.

*** ConfigMaps and Secrets

It is a good practice to make container images as reusable as possible. The same image should be able to be used for development, staging, and production. Which means, the same container should be flexible enough to be used on all the environments. This is where ConfigMaps and Secrets come into the picture. They allow us to specialize the image at runtime. 

ConfigMaps are used to provide configuration information for workloads. This can either be fine-grained information (a short string) or a composite value in the form of a file. 
Secrets are similar to ConfigMaps but focused on making sensitive information available to the workload. They can be used for things like credentials or TLS certificates.















* Katakoda
** Launch a single node
*** Minikube

Start a cluster with ~minikube start~

The debugging commands are extremely useful: ~kubectl cluster-info~

When you use ~kubectl run~, it creates a deployment.
Eg: ~kubectl run first-deployment --image=katacoda/docker-http-server --port=80~

Recall a deployment is just a higher level abstraction over ReplicaSets, which maintain a fixed sets of Pods running. 
Now, we can expose this deployment as a service with:
- either creating a service object and sending it to the apiserver
- or running the ~kubectl expose command~

~kubectl expose deployment first-deployment --port=80 --type=NodePort~

This will create a service and assign a node port to it
The dashboard is created in the kube-system namespace.

When the dashboard was deployed, it used externalIPs to bind the service to port 8443. 
For production, instead of externalIPs, it's recommended to use kubectl proxy to access the dashboard. 

A good debugging command: ~kubectl describe <object type> <object name>~ eg: ~kubectl describe pods mypod~
Earlier we ran the expose command:

~kubectl expose deployment http --external-ip="172.17.0.92" --port=8000 --target-port=80~
This :top: exposes the container port 80 to the host port 8000

Run and expose in a single command:
~kubectl run httpexposed --image=katacoda/docker-http-server:latest --replicas=1 --port=80 --hostport=8001~

The ports are opened on the pod, not on the container. And since all the containers in the pod share the same network namespace, they cannot run on the same port, so it's no problem.

Scaling the deployment will request Kubernetes to launch additional Pods. These Pods will then automatically be load balanced using the exposed Service.
You can scale a deployment (or a replicaset), with: ~k scale --replicas=10 myreplicaset_or_deployment~

After you create a deployment (or a replicaset), you can put a service on it. 

#+begin_src yaml
apiVersion: v1
kind: Service
metadata:
  name: webapp1-svc
  labels:
    app: webapp1
spec:
  type: NodePort
  ports:
  - port: 80
    nodePort: 30080
  selector:
    app: webapp1
#+end_src

Cluster IP is the default approach when creating a Kubernetes Service. The service is allocated an internal IP that other components can use to access the pods.

* General Notes

** RBAC

*** Talk by Eric, CoreOS - https://www.youtube.com/watch?v=WvnXemaYQ50

Everyone needs to talk to the api-server

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2018-09-02 10:17:16
[[file:assets/screenshot_2018-09-02_10-17-16.png]]


The api-server needs to authenticate everyone who talks to the api-server. After authentication (who is talking to me), we may want the api-server to have authorize powers also, that is we may want different permissions for different components. 

**** Authentication
Users in Kubernetes are just strings which are associated with the request through credentials. 
Eg:
#+begin_src
Username=darshanime
Groups=["developer", "gopher"]
#+end_src

How this information is pulled out can of any of:
- x509 client auth
- password files
- bearer token passwords
- etc

This is pluggable. You have to decide when you deploy your api-server, you need to pass these flags.
Eg:
#+begin_src
/usr/bin/kube-apiserver --client-ca-file=/etc/kubernetes/ca.pem ...
#+end_src

The cert that is generated by the api-server and given to the nodes, kubelet etc looks like so:

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2018-09-02 10:45:29
[[file:assets/screenshot_2018-09-02_10-45-29.png]]

The cert should be valid. Also, the cert has: CN (common name), which is thought of as the username for any request using this cert. 
We can use the O (organization) field to pull out group information, which can be later used for authorization.


Apart from x509, there are some other methods as well:
- token files
4190u4122u101-23123, darshanime,3,"developer,grophers"

:top:, if I show up with the first field as the Bearer token, I would be authenticated as darshanime with the group "developer,grophers"

There is webhook too, with this, the api-server sends the Authorization: Bearer <token> to another server to determine what the user is. GKE uses this to authenticate you on their Kubernetes service. 

All of these are completely managed outside Kubernetes. We have to set them when we setup the api-server. We have to say, I'll provision the api-server with these flags, these files on disk etc

**** Service Accounts
Service accounts are just bare token managed by Kubernetes. You can use these thru the api-server to create users as you want.

#+begin_src go
k create serviceaccount do-the-root
#+end_src

This will create a serviceaccount, which has some secrets.

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2018-09-02 10:55:16
[[file:assets/screenshot_2018-09-02_10-55-16.png]]


These secrets are the credentials that the serviceaccount uses to authenticate itself. +If your api-server is using tokens, this will be a token. If it is using x509 certs, it will be a certificate.+  It is a jwt token. 

You can mention the service account for each Kubernetes object (pod, deployment etc). If you do not give it a name, it will be set to the ~default~ service account. 
If you specify the serviceaccount, Kubernetes takes care of mounting the secrets onto your pod. 

~Secrets are tmpfs, they don't touch the disk, they are always in memory~

You can view it:

#+begin_src
$ k get serviceaccount default -o yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  creationTimestamp: 2018-08-31T11:08:12Z
  name: default
  namespace: default
  resourceVersion: "431"
  selfLink: /api/v1/namespaces/default/serviceaccounts/default
  uid: 26b959b9-ad0e-11e8-a2ea-027dc18e9b58
secrets:
- name: default-token-blvch


#+end_src

Here, we see that the default serviceaccount has a secret attached - default-token-blvch

We can view it:
#+begin_src
k8s (master) ✗ k get secrets -o yaml default-token-blvch
apiVersion: v1
data:
  ca.crt: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUMrVENDQWVHZ0F3SUJBZ0lKQVBXcHR6QkVqTzVSTUEwR0NTcUdTSWIzRFFFQkN3VUFNQkl4RURBT0JnTlYKQkFNTUIydDFZbVV0WTJFd0lCY05NVGd3T0RNeE1UQTFPREEwV2hnUE1qRXhPREE0TURjeE1EVTRNRFJhTUJJeApFREFPQmdOVkJBTU1CMnQxWW1VdFkyRXdnZ0VpTUEwR0NTcUdTSWIzRFFFQkFRVUFBNElCRHdBd2dnRUtBb0lCCkFRREN1eFdlMGo2dllwVTlieEFUYTN0ZVRIT0wxdGlTMStWYlRjbUsyZkVnb3Qwb212bjh6VkNqOEVtT0I1amUKaUNsQVhpNUtrMGRhcGJkbitxM2VzZXZ1dkYrYnEzUVJEdzJ5bnBMdnRpZlRqZ1hSY1l5cUlybjN3cVVQbUZhMQpaekNhTnpnOTluYTgxT2tmT0dPMmM4WEVhdkcyM09MV2hvSlE0SWVuZWZFWlNSTjNOVmdxaWVabzZiSjIzUUMyClFldjZFNS9kWnlsSDMrcUF5UTJOTGRmaHRlc2VkdlJiVzBzeHVtNExncU53bmNaa2R0cEhudXZvQjVpWDNZdFkKdWlZc2JQKzh0SHROSzBUY29WKzNGSjVhcDdYSUQwaTVKWEI4M0dTK0FtQ2RFbXZ2QUVVNlp2MVE5aG9ESllxQgpnbDl5YVNHMTB6azdPSnVhd1lCU29sbHhBZ01CQUFHalVEQk9NQjBHQTFVZERnUVdCQlFCNjRBTENpRXdpam1FCjV4UzYwZThtRFVFODZUQWZCZ05WSFNNRUdEQVdnQlFCNjRBTENpRXdpam1FNXhTNjBlOG1EVUU4NlRBTUJnTlYKSFJNRUJUQURBUUgvTUEwR0NTcUdTSWIzRFFFQkN3VUFBNElCQVFDaFhEaGc0WWx5NDhLY0hQeGU5OFRFTTRTVApYVWtiUFpwSW5aU2s1VTc4S1FWaGNTWm9nNnk2QXoyS3JPUlg4QmwrSDdGandtSVl6REwrdVk1SWtWVVJ3bVdsClhkUVAzdWNUOXpLd1RDNnRRWk95bElvQ1VDOFBxcCtTbC83Ym4rRVdSQ0dmZzM3RDlMem11blloWmFkd0doSjIKZTVSUm8vanIzL2FSSzlXUkduYzloSmRBSThjbnFabkRXbWRZUEFKTDZwVCtKTytYQmNxUXlrUC8vTFVIMlovYgo2NW9QUEdFQlJTdWxldDdLUkw2dHBQWkI2c3lMS1QxazV0TFdqZUZ0ZCtuSVQ1d0p3cUlybGZhcWF0NTEraWdkClZ0YTcxbGc4U1R5WEZ0Wmt5enNFa0VieU44OCswZnh0OGI1emo1RmdmMnMvQ1ZUOG5YOWxBK21WeHdaNQotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg==
  namespace: ZGVmYXVsdA==
  token: ZXlKaGJHY2lPaUpTVXpJMU5pSXNJbXRwWkNJNklpSjkuZXlKcGMzTWlPaUpyZFdKbGNtNWxkR1Z6TDNObGNuWnBZMlZoWTJOdmRXNTBJaXdpYTNWaVpYSnVaWFJsY3k1cGJ5OXpaWEoyYVdObFlXTmpiM1Z1ZEM5dVlXMWxjM0JoWTJVaU9pSmtaV1poZFd4MElpd2lhM1ZpWlhKdVpYUmxjeTVwYnk5elpYSjJhV05sWVdOamIzVnVkQzl6WldOeVpYUXVibUZ0WlNJNkltUmxabUYxYkhRdGRHOXJaVzR0WW14MlkyZ2lMQ0pyZFdKbGNtNWxkR1Z6TG1sdkwzTmxjblpwWTJWaFkyTnZkVzUwTDNObGNuWnBZMlV0WVdOamIzVnVkQzV1WVcxbElqb2laR1ZtWVhWc2RDSXNJbXQxWW1WeWJtVjBaWE11YVc4dmMyVnlkbWxqWldGalkyOTFiblF2YzJWeWRtbGpaUzFoWTJOdmRXNTBMblZwWkNJNklqSTJZamsxT1dJNUxXRmtNR1V0TVRGbE9DMWhNbVZoTFRBeU4yUmpNVGhsT1dJMU9DSXNJbk4xWWlJNkluTjVjM1JsYlRwelpYSjJhV05sWVdOamIzVnVkRHBrWldaaGRXeDBPbVJsWm1GMWJIUWlmUS5BcTh5VUQtQ0RWc0EzWlFydEQ2UlVmWGItUHVWX3lOMFA5alNVYjRoemEzNWtzXzgtVUNOeUtjenU3X0pxVmZXYnBqQjhLS1Q5d1lmSlMxczJaT3NIRmVLMXFRQ3VNRmdnZnpRWndpa01aMFlsV0ctd05NUkN6eHpUVjZtNTAwRTdDS09yTjZNOGsxZ0hsNjQxb3NPZW9pRWRqbEVJOUR6ZzktUVNzaHVMSkpGNnFTaFV4NDltLTBlMWVFY05BREpBV2p4M3ZGUDVYR3dfVWRPckM5RENxaWFidEtZekJuXy03NGtKTXF4RWh5NVJ3T0NEUFpqT1QzNkhlVkxQUWJGZzNjX0hmOUdzOWhxdUN0THl1UzA1Q0ZNRVZlU1NXWVJDNHJOd19NMEtRX0ZYbjkxUUtULU9ZeGEyLWVPTzhZSEtFMV9rR1RZbk9lWXZaNGVZcTY5VFE=
kind: Secret
metadata:
  annotations:
    kubernetes.io/service-account.name: default
    kubernetes.io/service-account.uid: 26b959b9-ad0e-11e8-a2ea-027dc18e9b58
  creationTimestamp: 2018-08-31T11:08:12Z
  name: default-token-blvch
  namespace: default
  resourceVersion: "429"
  selfLink: /api/v1/namespaces/default/secrets/default-token-blvch
  uid: 26bb51fb-ad0e-11e8-a2ea-027dc18e9b58
type: kubernetes.io/service-account-token
#+end_src

The token is base64 encoded, it can be decoded and seen on jwt.io. It shows information like:

#+begin_src json
{
  "iss": "kubernetes/serviceaccount",
  "kubernetes.io/serviceaccount/namespace": "default",
  "kubernetes.io/serviceaccount/secret.name": "default-token-blvch",
  "kubernetes.io/serviceaccount/service-account.name": "default",
  "kubernetes.io/serviceaccount/service-account.uid": "26b959b9-ad0e-11e8-a2ea-027dc18e9b58",
  "sub": "system:serviceaccount:default:default"
}
#+end_src

We can see the serviceaccount name, the serviceaccount uuid, serviceaccount namespace etc
They can be used outside the cluster. So, if anyone has your serviceaccount tokens, it can act on behalf of your serviceaccount and talk to the api-server

**** Authorization

It uses the usernames and group names that we pulled out of the authentication phase and uses that for authorization. 

One of the plugins for authorization is RBAC - role based access control.
It was made by RedHat in OpenShift.


Overview:
- default: deny all
- contain a subject, verb, resource, and namespace.
  - eg: user A can create pods in namespace B
- cannot
  - +refer to a single object in a namespace+
  - +refer to arbitrary fields in a resource+
- can
  - refer to sub resources (eg, node/status)

The RBAC apigroup has 4 top level objects:
- Role
- RoleBinding
- ClusterRole
- ClusterRoleBinding

**** Roles vs Bindings
Roles declare a set of powers. 
Bindings "bind" users or groups to those powers (roles).
So, you can have a role called "admin" that can do anything. 
Then you can give (bind) this power (role) to the user darshanime for eg

#+begin_src yaml
apiVersion: rbac.authorization.k8s.io/v1alpha1
kind: ClusterRole
metadata:
  name: secret-reader
rules:
  - apiGroups: [""] # v1 namespace
    resources: ["secrets"]
    verbs: ["get", "watch", "list"]
#+end_src

Here, :top: we defined a role which can read secrets. 
Now, we can bind a user to the role.

#+begin_src yaml
apiVersion: rbac.authorization.k8s.io/v1alpha1
kind: ClusterRoleBinding
metadata:
  name: read-secrets
subjects:
  - kind: Group # may be User, Group, or ServiceAccount
    name: manager
roleRef:
  kind: ClusterRole
  name: secret-reader
  apiVersion: rbac.authorization.k8s.io/v1alpha1
#+end_src

Here, we say that anyone in the "group" manager, has this power.

**** ClusterRoles vs Roles
Objects in Kubernetes can be either namespaced, or not. Eg, pods are namespaced, nodes are clusterlevel, they are not namespaced.

Roles can exist either at the namespace level or at a cluster level (using * namespace?)

Cluster role - manage one set of roles for your entire cluster -> define once, assign to any one in any namespace

Role: allow "namespace admins" to admin roles just for a namespace -> are local to the namespace. Can't create once, use for every namespace

Similarly, we have ClusterRoleBindings, vs. RoleBindings
ClusterRoleBindings can refer to only ClusterRoles, they offer cluster wide powers.
RoleBindings grant power within a namespace - they can refer to Roles, ClusterRoles

Consider:
Let's say I want to give coworker A the power to administer a particular namespace. Steps:
- create a ClusterRole
  - that is the namespace admin
- create a RoleBinding
  - for that namespace referring to the ClusterRole

So, we have 3 cases:

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2018-09-02 11:38:06
[[file:assets/screenshot_2018-09-02_11-38-06.png]]

Here, we have a ClusterRole, which has powers thru out the cluster, and we assign them using ClusterRoleBinding

They now have power thru out the cluster, like delete node etc

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2018-09-02 11:39:40
[[file:assets/screenshot_2018-09-02_11-39-40.png]]

Here, a central admin is creating ClusterRole, but it is bound only for a particular namespace, so the guy has admin privileges over the namespace. 

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2018-09-02 11:41:01
[[file:assets/screenshot_2018-09-02_11-41-01.png]]

This is when I have a rolebinding to create new roles applicable only within the given namespace.

This separation between ClusterRole and Role has builtin escalation prevention. If I do not have the privilege to read secrets, I cannot give someone a role that can read secrets. 

To bootstrap the cluster, the api-server has a cluster-admin role for core components like kubelet etc

Apart from RBAC, we have other plugins -> webhook, policy file etc



*** TLS with Go - Eric, https://ericchiang.github.io/post/go-tls/

There are several different ideas here

**** Public and Private key encryption

Whenever we want to prove our identity, for eg, when sshing etc, we can use the private-public key cryptography. 
The public key is public and the private key can be used to prove you are the owner of that public key, or in other words, you are who the public key says you are.

*Things encrypt with a public key can only be decrypted by its paired private key.*

So, how we can make use of this :top: fact is, 
I can encrypt some text using your public key. It can be decrypted only by someone who has the private key. If you have it, you can decrypt it and if you are able to say the phrase back to me, I can confirm that you are who you say you are.

*The cool part about this is you can prove you hold a private key without ever showing it to somebody.*


**** Digital Signatures

A free feature of the public private key pair is that we can digitally sign content. This sign can be used to ensure the validity of the document.

How we do this is, we take the document, hash it (say thru SHA256), then the private key computes a signature of the hashed document. This document can now be sent wherever.

The public key can then confirm, if its private key combined with a particular hash would have created that signature.

So, when you send the document, you can also send it's signature. The receiver can take your public key and verify you sent the document and it has not been tempered with.

This can be useful for a certificate authority, who wants to be able to distribute documents which can't be altered without everyone detecting. 

**** x509 certs

*Certificates are public keys wit some attached information (like what domains they work for)*

In order to create a certificate, we need to both specify that information and provide a public key.

So, we first start with creating a public-private key pair. We can call the private key the rootKey. 
Now, we can make a certificate. 

Certificates must be signed by the private key of a parent certificate -> like we talked in Digital Signature

To create the certificate, we need:
- the information to be put in the certificate, like the Subject, validity dates, etc
- the public key we want to wrap
- the parent certificate
- the parent’s private key

If we don't have the parent, then we use self's private key - this becomes a self signed certificate

Remember, the certificate is just a public key, with some extra information is all.

To prove ownership of a certificate you must have it's private key as well. This is why we need to have the private key inside our servers.

Now, from this cert (from this cert's private key), we can make more certs.

If you put this cert on a webserver and make requests to it, the client (browser, curl etc) will reject it because none of the public keys trusted by the client validate the digital signature provided by the certificate from the webserver. 

**** Getting the Client to Trust the Server
Let's mimic a situation where a certificate authority provides a organization with a cert for their website. 
For this, we can pretend that the rootCert we created above :top: belongs to a certificate authority, and we'll attempt to create another certificate for our server

Steps:
- create a public private key pair
- we will create the cert using:
  - some information about the expiry etc
  - public key generated
  - rootKey (the private key of the parent certificate) -> this will sign the certificate

To have the client trust the certificate, we need to ask it to trust "the public key that can validate the certificate's signature". Since we used the root key-pair to sign the certificate, if we trust the rootCert (it's public key), we'll trust the server's certificate.

**** Getting the Server to trust the client
This is exactly similar to the server side. We create a public-private key pair for the client, then we create a certificate for the client. On making the tcp connection, during the TLS handshake, we can present our certificate and the server can be configured to complete the handshake and accept the connection only if the certificate is valid.

*** RBAC with Joe Beda - https://www.youtube.com/watch?v=slUMVwRXlRo

Our kubectl talks to the api-server via TLS, so but it uses a self signed cert. So, in the kubeconfig file, we have the certificate data that we ask the kubectl to trust. When we switch it off, using --insecure-skip-tls-verify, what we do is we communicate to the api-server without verifying it's certificate. So, we could be talking to some over api-server if we are attacked without knowing it.

The api-server plays the role of the CA in the cluster. It issues new certs to service accounts etc

Also, for authenticating, the kubeconfig has ~users~, with the name and client-certificate-data, client-key-data

In the heptio aws quickstart, the kubectl creates an TLS connection to TCP ELB, it can do it because it knows the root cert to trust.
The QuickStart creates an ELB first, puts the elb's name in the SAN (subject alternate name), so the ELB can legitimately offer that cert. 
Then we give the kubectl cert to prove who we are, and that we are allowed to do that action that we are doing.


Service Accounts are cool, butt they are mostly about something running on Kubernetes to talk to api-server. Authorization (like x509, static passwords, static tokens, webhooks, authentication proxy (like we have for logs at draup) etc) are about accessing Kubernetes from outside.


We can mint certificates using the CA (the api-server). Other options are: CFSSL (cloudflare ssl certificate), OpenSSL, Hashicorp Vault etc. Then it's up to you how you want to sign them, you can use either of the above options.

For ease, Kubernetes has a built in CA in the api-server, which is used to automatic bootstrapping and certificate rotation for core components, but it can also mint certs for users. 


**** Adding a new user to the cluster
1. Generate a public-private key pair: (here, only the private key is generated. It is easy to go from private key to public key)
~openssl genrsa -out jakub.pem 2048~

And the csr - certificate signing request
~openssl req -new -key jakub.pem -out jakub.csr -subj "/CN=jakub"\~
We can add organization:
~openssl req -new -key jakub.pem -out jakub.csr -subj "/CN=users:jakub/O=cool-people"\~

The username lives in the common name.

Now, in the earlier post Eric wrote, we did not have a certificate signing request because we signed the certificate with our own root certificate.
In real life, we create a certificate signing request using our private key. Here, we provide the information like user, organization etc. The CA will add expiration etc. 

The CA can verify that the info in the CSR is accurate, and sign the certificate using the it's (the CA's) private key. 

In Kubernetes, we will base64 encode the csr and ~kubectl apply -f~ it

We can approve the CSR using ~k certificate approve <csr-name>~

Now, we should be able to download the certificate.
Now, you have the certificate and the private key. We can put this in the kubeconfig and we are good to go on the authentication front. 

**** Authorization
Authorization has several plugins (by plugins, we mean we have an interface that we expose and everyone implements it) - apac, webhook, Node Authorization, RBAC

There are 4 different things
- Role
- ClusterRole
- RoleBindings
- ClusterRoleBidings

Resources like nodes, csrs don't belong to a namespace -> they are cluster wide resources. 

Since roles belong to namespaces, you can do:
~k get roles --all-namespaces~

#+begin_src
 k8s (master) ✗ k get roles --all-namespaces
NAMESPACE     NAME                                             AGE
kube-public   system:controller:bootstrap-signer               1d
kube-system   extension-apiserver-authentication-reader        1d
kube-system   kubernetes-dashboard-minimal                     1d
kube-system   system::leader-locking-kube-controller-manager   1d
kube-system   system::leader-locking-kube-scheduler            1d
kube-system   system:controller:bootstrap-signer               1d
kube-system   system:controller:cloud-provider                 1d
kube-system   system:controller:token-cleaner                  1d
#+end_src

There are also clusterroles, ~k get clusterroles~

You can view then in detail:

#+begin_src yaml
k8s (master) ✗ k get clusterroles system:basic-user -o yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  annotations:
    rbac.authorization.kubernetes.io/autoupdate: "true"
  creationTimestamp: 2018-08-31T11:06:46Z
  labels:
    kubernetes.io/bootstrapping: rbac-defaults
  name: system:basic-user
  resourceVersion: "59"
  selfLink: /apis/rbac.authorization.k8s.io/v1/clusterroles/system%3Abasic-user
  uid: f38738a4-ad0d-11e8-a2ea-027dc18e9b58
rules:
- apiGroups:
  - authorization.k8s.io
  resources:
  - selfsubjectaccessreviews
  - selfsubjectrulesreviews
  verbs:
  - create

-------
 k8s (master) ✗ k get roles system::leader-locking-kube-scheduler -n kube-system -o yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  annotations:
    rbac.authorization.kubernetes.io/autoupdate: "true"
  creationTimestamp: 2018-08-31T11:06:48Z
  labels:
    kubernetes.io/bootstrapping: rbac-defaults
  name: system::leader-locking-kube-scheduler
  namespace: kube-system
  resourceVersion: "178"
  selfLink: /apis/rbac.authorization.k8s.io/v1/namespaces/kube-system/roles/system%3A%3Aleader-locking-kube-scheduler
  uid: f49fadb3-ad0d-11e8-a2ea-027dc18e9b58
rules:
- apiGroups:
  - ""
  resources:
  - configmaps
  verbs:
  - watch
- apiGroups:
  - ""
  resourceNames:
  - kube-scheduler
  resources:
  - configmaps
  verbs:
  - get
  - update
#+end_src 

They have similar structure. We mention the api group, the resource type and the verb.

Apart from these roles that Kubernetes ships with, we can create our own roles.

ClusterRoleBinding - I'm going to let this user do this thingy across every name space in the cluster.

RoleBinding - I'm going to let this user do this thingy in this ONE name space in the cluster.

~kubectl~ has a ~--dry-run~ flag, with the ~-o yaml~ to get the yaml. Do this to jump start the yaml if needed.

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2018-09-02 16:19:40
[[file:assets/screenshot_2018-09-02_16-19-40.png]]

Here, :top:, we are doing:

~kubectl create rolebinding joe --clusterrole=admin --user=users:joe~
So, we are assigning the clusterrole admin to joe on the default namespace. (default since we aren't explicitly mentioning any namespace)

Now, if we create a new namespace, and run a pod there, we won't be able to use the user joe to view the pods there.

**** Service Accounts
This is a robotic identity for the apps running on the cluster. 
They operate just like Role and ClusterRoles, but not for users. They are for applications.

Each namespace has a ~default~ service account

So, example:
This will give the ~default~ service account admin role for a particular namespace
#+begin_src
kubectl create rolebinding varMyRoleBinding --clusterrole=admin --serviceaccount=varMyNamespace:default --namespace=varMyNamespace
#+end_src

To give the service account admin access to the whole cluster.

#+begin_src
kubectl create clusterrolebinding varMyClusterRoleBinding --clusterrole=cluster-admin --serviceaccount=kube-system:default
#+end_src

*** Configuring RBAC for Helm - https://docs.helm.sh/using_helm/#role-based-access-control

Helm has 2 components, (v2.10), helm the client, and tiller the server side component. 

We can have various ways to configure rbac for tiller.
We need to create a service account for the tiller component. SAs live in a namespace.

**** Granting cluster admin to tiller
This is the simplest to accomplish. 
We already have a clusterrole called ~cluster-admin~. We can create a service account tiller-sa and just assign it to that sa.

#+begin_src yaml
api: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
#+end_src

Now, we can assign the clusterrole ~cluster-admin~ using the clusterrolebinding
#+begin_src yaml
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: tiller # this is just metadata - the name of this clusterrolebinding, doesn't matter
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin # we way, we want to bind the cluster-admin cluster role
subjects:
  - kind: ServiceAccount # to the tiller service account which lives in the kube-system namespace, could be user etc here?
    name: tiller 
    namespace: kube-system
#+end_src

I followed this and got a tiller pod running

#+begin_src 
$ k get po --all-namespaces
NAMESPACE     NAME                                      READY     STATUS    RESTARTS   AGE
kube-system   tiller-deploy-78779c79d4-884tk            1/1       Running   0          1m
#+end_src

Doing a describe on the pod showed a secret
#+begin_src 
k describe pods tiller-deploy-78779c79d4-884tk -n kube-system
Priority:           0
...
Volumes:
  tiller-sa-token-62kn4:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  tiller-sa-token-62kn4
    Optional:    false
QoS Class:       BestEffort
...
#+end_src

We can see the secret: 
#+begin_src 
k get secrets -o yaml tiller-sa-token-62kn4 -n kube-system
# take the token from the output, base64 --decode it and check on jwt.io to get this:
{
  "iss": "kubernetes/serviceaccount",
  "kubernetes.io/serviceaccount/namespace": "kube-system",
  "kubernetes.io/serviceaccount/secret.name": "tiller-sa-token-62kn4",
  "kubernetes.io/serviceaccount/service-account.name": "tiller-sa",
  "kubernetes.io/serviceaccount/service-account.uid": "14d98272-aeb2-11e8-800b-0261adf852f0",
  "sub": "system:serviceaccount:kube-system:tiller-sa"
}

# the output also has the SA's certificate
# the certificate decoder on sslshopper.com shows this:
Certificate Information:
Common Name: kube-ca
Valid From: August 31, 2018
Valid To: August 7, 2118
Serial Number: 17701881228292845137 (0xf5a9b730448cee51)
#+end_src

**** Deploy tiller in a namespace
We can replace ClusterRoleBinding with RoleBinding and restrict tiller to a particular namespace.

Creating a namespace
#+begin_src yaml
apiVersion: v1
kind: Namespace
metadata:
  name: tiller-world
#+end_src

Creating the service account
#+begin_src yaml
api: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
#+end_src

Now, we can create a role that allows tiller to manage all resources in the tiller-world namespace.

#+begin_src yaml
apiVersion: rbac.authroization.k8s.io/v1beta1
kind: Role
metadata:
  name: tiller-manager # name for our Role
  namespace: tiller-world # namespace for which the role exists
rules:
- apiGroups: ["", "batch", "extensions", "apps"] # apis the role gives access to 
  resources: ["*"] # resources in the api groups that the role gives access to
  verbs: ["*"] # verbs the role gives access to
#+end_src

Finally, we can create a RoleBinding to marry the two
#+begin_src yaml
apiVersion: rbac.authroization.k8s.io/v1beta1
kind: RoleBinding
metadata:
  name: tiller-binding # name for the RoleBinding
  namespace: tiller-world
subjects:
- kind: ServiceAccount 
  name: tiller
  namespace: tiller-world # name for the ServiceAccount
roleRef:
  kind: Role
  name: tiller-manager # name for the Role
  apiGroup: rbac.authorization.k8s.io
#+end_src
Now we can init helm with the service account and namespace

~helm init --service-account tiller --tiller-namespace tiller-world~

Run it using: ~$ helm install nginx --tiller-namespace tiller-world --namespace tiller-world~

**** Deploy tiller in and restrict deploying to another namespace

In the example above, we gave Tiller admin access to the namespace it was deployed inside. 
Now we will limit Tiller’s scope to deploy resources in a different namespace!

For example, let’s install Tiller in the namespace myorg-system and allow Tiller to deploy resources in the namespace myorg-users.
Like before, creating the namespace and serviceaccount
#+begin_src
$ kubectl create namespace myorg-system
namespace "myorg-system" created
$ kubectl create serviceaccount tiller --namespace myorg-system
serviceaccount "tiller" created
#+end_src

Now, defining a role that gives tiller privileges to manage all resources in myorg-users namespace.

#+begin_src yaml
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: Role
metadata:
  name: tiller-manager
  namespace: myorg-users
rules:
- apiGroups: ["", "extension", "apps"]
  resources: ["*"]
  verbs: ["*"]
#+end_src

Now we can create a RoleBinding to marry the Role (tiller-manager) with SA (tiller)

#+begin_src yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: tiller-binding
  namespace: myorg-users
subjects:
- kind: ServiceAccount
  name: tiller
  namespace: myorg-system
roleRef:
  kind: Role
  name: tiller-manager
  apiGroup: rbac.authorization.k8s.io
#+end_src

One more thing, we need to give tiller access to read configmaps in myorg-system so that it can store release information. 

#+begin_src yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  namespace: myorg-system
  name: tiller-manager
rules:
- apiGroups: ["", "extensions", "apps"]
  resources: ["configmaps"]
  verbs: ["*"]
#+end_src

#+begin_src yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: tiller-binding
  namespace: myorg-system
subjects:
- kind: ServiceAccount
  name: tiller
  namespace: myorg-system
roleRef:
  kind: Role
  name: tiller-manager
  apiGroup: rbac.authorization.k8s.io
#+end_src

* Istio

Istio is a service mesh. When you deploy microservices to cloud platforms, you want to control the interaction between the services. You may want:
- explicit rules like, frontend should talk to backend only, the frontend cannot talk to database etc
- to roll out a new version and test perform A/B testing on it, with say 1% of the traffic. So, you want to route 1% of the network traffic on a service to this new version.
- to mirror the entire traffic to a new version of the service to check out how it performs under production load before deploying it
- to set timeouts, retries on requests so that you get circuit breaking features etc (like Netflix's Hystrix, GoJek's Heimdall)


Istio helps with all that :top:

It basically injects a sidecar container in all your pods (running ~envoy~, which is a proxy server, like we can operate nginx) and hijacks the network. Everything goes thru istio(via envoy) now. This allows it to control the network at a very fine grained level, like setting the network rules, timeouts, retries etc. 
All the features are implemented by the envoy proxy, istio makes it accessible and integrated with COs (cloud orchestrators)

This act of hijacking the network, by having an agent running along all the containers forms a "service mesh"
~The term service mesh is used to describe the network of microservices that make up such applications and the interactions between them~

Istio documentation says:

#+BEGIN_QUOTE
As a service mesh grows in size and complexity, it can become harder to understand and manage. Its requirements can include discovery, load balancing, failure recovery, metrics, and monitoring. A service mesh also often has more complex operational requirements, like A/B testing, canary releases, rate limiting, access control, and end-to-end authentication.
#+END_QUOTE

We manage and configure Istio using its control plane.
#+BEGIN_QUOTE
Istio is platform-independent and designed to run in a variety of environments, including those spanning Cloud, on-premise, Kubernetes, Mesos, and more. You can deploy Istio on Kubernetes, or on Nomad with Consul.
#+END_QUOTE

Istio is designed to be customizable, and can be extended, and integrated with existing solutions for ACLs, logging, monitoring etc.

Istio has a sidecar injector, which looks out for new pods and automatically injects envoy sidecar in the pod. It works by registering the sidecar injector as a admission webhook which allows it to dynamically change the pod configuration before the pod starts. 
 
** Architecture
The Istio service mesh is logically split into a data plane and control plane

- data plane
  - consists of a set of intelligent proxies deployed as sidecars which mediate and control all network communication b/w microservices.
  - it also has Mixer which is a general purpose policy and telemetry hub (so it gets resource usage (cpu, ram) etc)
- control hub
  - manages and configures the components in the data plane - the proxy and mixers



#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2018-09-10 11:15:33
[[file:assets/screenshot_2018-09-10_11-15-33.png]]

Note here, Service A is trying to talk to Service B. The traffic goes thru envoy proxy. It is allowed only after the policy checks are passed by the Mixer in the Control Plane API.

The control plane also has Pilot, and Citadel

** Istio Components
*** Envoy
Envoy is an high performance C++ proxy designed to be used as a sidecar container in service meshes.
Istio uses many builtin envoy features like:
- dynamic service discovery
- load balancing
- tls termination
- http/2 and grpc proxies
- circuit breakers
- health checks
- staged rollouts with %based traffic split
- fault injection
- rich metrics

These features allow istio to extract a wealth of signals about traffic behavior as ~attributes~, which can be used to enforce policy decisions, and monitoring purposed.

*** Mixer
Istio’s Mixer component is responsible for policy controls and telemetry collection. It provides backend abstraction and intermediation - ie it abstracts away the rest of Istio from the implementation details of individual infrastructure backends. 
It takes the attributes provided by envoy and enforces the policy.

Mixer includes a flexible plugin model. This model enables istio to interface with a variety of host environments and infrastructure backends. This, Mixer abstracts envoy proxy and istio managed services from these details

*** Pilot
Pilot provides service discovery, traffic management capabilities for intelligent routing (A/B, canary deployments), resiliency etc.
So, Pilot is used as in, it directs the packets across the service mesh. The Mixer says this route is allowed, the Pilot takes over from there. It dictates the timeout, the retries, the circuit breaking etc. 

Pilot consumes high level routing rules provided by the user from the control plane and propagates them to the sidecars at runtime.

Pilot abstracts platform specific service discovery mechanisms for both operator and proxy (like CRI-O). So now, the operator (cluster administrator) can use the same interface for traffic management across any CO - this is like the operator is given the CRI interface. 
If any project wants to be used as a proxy for Istio (like maybe Nginx), they just have to implement the Envoy data plane APIs and istio will be happy to talk to them.

*** Citadel
It provides service-to-service and end-user authentication with built-in identity and credential management. 

#+BEGIN_QUOTE
You can use Citadel to upgrade unencrypted traffic in the service mesh. Using Citadel, operators can enforce policies based on service identity rather than on network controls. Starting from release 0.5, you can use Istio’s authorization feature to control who can access your services.
#+END_QUOTE

*** Galley
It validates user authored Istio API configuration. Over time it will be at the forefront of the Control Plane and will be responsible for configuration ingestion, processing and distribution component of Istio. It will be the sole interface to the user accessing the control plane.


** Traffic Management

Istio's traffic management essentially decouples traffic flow and infrastructure scaling. This allows us to specify via Pilot the traffic rules which we want, rather than saying which specific pods/VMs should receive traffic. 

#+BEGIN_QUOTE
For example, you can specify via Pilot that you want 5% of traffic for a particular service to go to a canary version irrespective of the size of the canary deployment, or send traffic to a particular version depending on the content of the request.
#+END_QUOTE

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2018-09-10 11:58:53
[[file:assets/screenshot_2018-09-10_11-58-53.png]]


*** Pilot and Envoy
#+BEGIN_QUOTE
The core component used for traffic management in Istio is Pilot, which manages and configures all the Envoy proxy instances deployed in a particular Istio service mesh. 
Pilot lets you specify which rules you want to use to route traffic between Envoy proxies and configure failure recovery features such as timeouts, retries, and circuit breakers.
It also maintains a canonical model of all the services in the mesh and uses this model to let Envoy instances know about the other Envoy instances in the mesh via its discovery service.
#+END_QUOTE

Each envoy instance has the load balancing information from Pilot, so it intelligently distributes traffic to the right place (following its specified routing rules), performs health checks etc

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2018-09-10 12:02:58
[[file:assets/screenshot_2018-09-10_12-02-58.png]]

Pilot abstracts the CO from envoy proxy and the user. To envoy, it exposes the Envoy API and to the user, it employs the Rules API.

Want to be used by Istio as proxy? Implement the Envoy API
Want to be used by Istio to configure traffic rules in the CO? Contribute to Istio a platform adapter.

Istio maintains a canonical representation of services in the mesh that is independent of the underlying platform - in the Abstract Model layer. 

For eg, the Kubernetes adapter (eg of a platform adapter) in Pilot implements the necessary controllers to watch the Kubernetes API server for changes to pod registration information, ingress resources, 3rd party resources that store traffic management rules etc.
This data is translated into the canonical representation. An Envoy-specific configuration is then generated based on the canonical representation.


You can specify high-level traffic management rules through Pilot’s Rule configuration. These rules are translated into low-level configurations and distributed to Envoy instances via the process outlined above. Once the configuration is set on the envoy instance, it doesn't need to talk to Pilot. So, the Pilot just configures envoy and then it gets working on it's own.

So, essentially:

High level rules -> abstract model -> envoy configuration -> envoy


Istio in it's canonical model of the services, maintains a version of all the services it has. It isa finer-grained way to subdivide services. You can specify the traffic routing rules based on the service versions to provide additional control over traffic b/w services. 

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2018-09-10 12:14:41
[[file:assets/screenshot_2018-09-10_12-14-41.png]]

Note how the user is putting the rule using the Rules API component of Pilot. It is converting that to envoy configuration and sending it over. The envoy starts respecting it immediately since it is a built in feature for it.
Routing rules allow Envoy to select a version based on conditions such as headers, tags associated with source/destination, and/or by weights assigned to each version.

So, one way to describe Istio is: ~Istio is just a platform agnostic way to configure Envoy proxy.~

Istio does not provide a DNS. Applications can try to resolve the FQDN using the DNS service present in the underlying platform (kube-dns, mesos-dns, etc.).

*** Ingress and Egress

Since all inbound and outbound (ingress and egress) traffic is proxied thru the envoy side car, we can add retries, timeout, circuit breaking etc to the traffic and obtain detailed metrics on the connections to these services.

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2018-09-10 12:54:56
[[file:assets/screenshot_2018-09-10_12-54-56.png]]

*** Failure handling
Istio’s traffic management rules allow you to set defaults for failure recovery per service and version that apply to all callers.

Note, this is why we put the retry defaults in the virtualservice for draup services

#+begin_src yaml
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: foobar-vs
spec:
  hosts:
  - "*.foobar.com"
  http:
  - timeout: 3s
    route:
      - destination:
          host: foobar-se
        weight: 100
    retries:
      attempts: 3
#+end_src

#+BEGIN_QUOTE
However, consumers of a service can also override timeout and retry defaults by providing request-level overrides through special HTTP headers. With the Envoy proxy implementation, the headers are ~x-envoy-upstream-rq-timeout-ms~ and ~x-envoy-max-retries~, respectively.
#+END_QUOTE

** Policies and Telemetry

#+BEGIN_QUOTE
Istio provides a flexible model to enforce authorization policies and collect telemetry for the services in a mesh.
They include such things as access control systems, telemetry capturing systems, quota enforcement systems, billing systems, and so forth. Services traditionally directly integrate with these backend systems, creating a hard coupling and baking-in specific semantics and usage options.
#+END_QUOTE

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2018-09-10 13:27:02
[[file:assets/screenshot_2018-09-10_13-27-02.png]]



The Envoy sidecar logically calls Mixer before each request to perform precondition checks, and after each request to report telemetry. 

The sidecar has local caching such that a large percentage of precondition checks can be performed from cache. Additionally, the sidecar buffers outgoing telemetry such that it only calls Mixer infrequently.

Mixer has 2 components from a high level:
- Backend abstraction
  - it abstracts away the backend and provides the rest of istio with a consistent abstraction of the backend
- Intermediation
  - Mixer allows operators(cluster admin) to have fine grained control b/w the mesh and infrastructure backends

Mixer also has reliability and scalability benefits. Policy enforcement and telemetry collection are entirely driven from configuration. 

*** Adapters
#+BEGIN_QUOTE
Mixer is a highly modular and extensible component. One of its key functions is to abstract away the details of different policy and telemetry backend systems, allowing the rest of Istio to be agnostic of those backends.
#+END_QUOTE

Note, it supports plugins for both policy and telemetry backend systems. 


#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2018-09-10 14:23:45
[[file:assets/screenshot_2018-09-10_14-23-45.png]]



* CSI - Container Storage Interface

** RPCs
RPC allows one computer to call a subroutine in another computer. It is a high level model for client-server communication.

In a microservices, one could use RPCs and not HTTP APIs since RPCs have several advantages over HTTP APIs.

*** History
In the early days, there were protocols that were great for human to application communication, like Email etc, but none for computer-computer application protocols. 

Just like we have procedure calls, (which are function calls), we have remote procedure calls which call the procedures that aren't on the same machine, but are remote. 

The idea was that, since in a local procedure call, the compiler gives us the ability to make the call, the idea was that the compiler could play a role in enabling remote procedure calls as well.


*** General
When the client sends a RPC, it blocks till the server sends the response back. The server receives the request and starts process execution. 
There has to be some code on the client machine that knows this is a RPC and makes the needed network communication and get the answer and present it as the return value of the procedure call.

The RPC framework knows which server to contact, which port to contact, how to serialize the call, marshal it's arguments etc. On the server side, similar stub should be present to unmarshall the arguments etc.

** Intro to gRPC - https://www.youtube.com/watch?v=RoXT_Rkg8LA
We know how apps talk to one another. It is SOAP, REST (HTTP + JSON). REST is just a architectural principle, about how to structure your API when you use HTTP+JSON etc.

REST is not that great, actually. It has some advantages:
- easy to understand - text based protocols
- great tooling to inspect and modify etc
- loose coupling b/w client and server makes changes relatively easy
- high quality implementation in every language.

It has disadvantages as well:
- No formal API contract - there is documentation (swagger etc), but no formal contract
- Streaming is difficult
- Bidirectional streaming not possible (that's why we had to invent websockets etc)
- Operations are difficult to model ("restart the computer", should that be a POST, GET call?)
- Many times your services are just HTTP endpoints, and don't follow the REST principles nicely.
- Not the most efficient since we are using gRPC


GRPC solves all these problems. You first define a contract using GRPC IDL - GRPC Interface Definition Language. 

#+begin_src
service Identity {
  rpc GetPluginInfo(GetPluginInfoRequest)
    returns (GetPluginInfoResponse) {}

  rpc GetPluginCapabilities(GetPluginCapabilitiesRequest)
    returns (GetPluginCapabilitiesResponse) {}

  rpc Probe (ProbeRequest)
    returns (ProbeResponse) {}
}

message GetPluginInfoResponse {
  // The name MUST follow reverse domain name notation format
  // (https://en.wikipedia.org/wiki/Reverse_domain_name_notation).
  // It SHOULD include the plugin's host company name and the plugin
  // name, to minimize the possibility of collisions. It MUST be 63
  // characters or less, beginning and ending with an alphanumeric
  // character ([a-z0-9A-Z]) with dashes (-), underscores (_),
  // dots (.), and alphanumerics between. This field is REQUIRED.
  string name = 1;

  // This field is REQUIRED. Value of this field is opaque to the CO.
  string vendor_version = 2;

  // This field is OPTIONAL. Values are opaque to the CO.
  map<string, string> manifest = 3;
}
#+end_src

Here, we have define a ~Identity~ service, which supports 3 procedure calls - ~GetPluginInfo~, ~GetPluginCapabilities~, ~Probe~. 
See how the ~GetPluginInfo~ takes in the ~GetPluginInfoRequest~ and returns a ~GetPluginInfoResponse~. Then we defined what the ~GetPluginInfoResponse~ looks like below that.

This is a formal definition, with types. We can run a compiler thru it. 

~protoc --proto_path=. --python_out=plugins-grpc:./py calls.proto~

This will generate client side code that can call the RPC. 
Similarly, we can generate the server side code as well.

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2018-09-16 13:58:12
[[file:assets/screenshot_2018-09-16_13-58-12.png]]


Grpc is the framework which makes the RPC possible, it is a implementation of the RPC protocol.

GRPC is a protocol built on top of HTTP/2 as the transport protocol. The messages that you send and receive are serialized using Protocol Buffers - you can use some other. 

Clients open one long lived connection to a grpc server. A new HTTP/2 stream for each RPC call. This allows multiple simultaneous inflight RPC calls. Allows client side and server side streaming. 

The compiler can generate stubs in 9 languages. The reference implementation of grpc is in C. Ruby, Python are all bindings to the C core.

Gprc supports plugabble middleware, on the client and server side which can be used to do logging etc. 

Swagger solves some of the problems around contract, in that people can make swagger IDL like contracts, but it is still text based protocol, doesn't solve bidirectional streaming. Also, the swagger IDL is verbose. 

One problem is that you can't call this from the browser. The fact that it relies on having an intimate control over the HTTP2 connection means you need to have a shim layer in between.

Many companies have exposed grpc APIs to the public - like Google Cloud (their pub sub api, speech recognition api) etc.

** gRPC - https://www.youtube.com/watch?v=OZ_Qmklc4zE

Grpc - gRPC Remote Procedure Calls. GRPC by itself is agnostic of schema, only when it is used with protocol buffers, it has enforced schema.

It is a recursive fullform. It is a open source, high performance "RPC framework"

It is the next generation of Snubby RPC build and used inside Google. 


*** Getting Started
- defining a service in a ~.proto~ file using protocol buffers IDL
- generate the client and server stub using the protocol buffer compiler
- extend the generated server class in your code to fill in the business logic
- invoke it using the client stubs

*** An aside: Protocol Buffers
Google's lingua franca for serializing data - RPCs and storage. It is binary (so compact), structures can be extended in backward and forward compatible ways.

It is strongly typed, supports several data types


*** Example
Let's write an example called Route Guide. There are clients traveling around and talking to a central server. Or, it can be 2 friends traveling along 2 different routes and talking to each other.

We have to decide: what types of services do we need to expose? What messages to send?

#+begin_src
syntax = "proto3";

// Interface exported by the server.
service RouteGuide {
  // A simple RPC.
  //
  // Obtains the feature at a given position.
  //
  // A feature with an empty name is returned if there's no feature at the given
  // position.
  rpc GetFeature(Point) returns (Feature) {}

  // A Bidirectional streaming RPC.
  //
  // Accepts a stream of RouteNotes sent while a route is being traversed,
  // while receiving other RouteNotes (e.g. from other users).
  rpc RouteChat(stream RouteNote) returns (stream RouteNote) {}
}

// Points are represented as latitude-longitude pairs in the E7 representation
message Point {
  int32 latitude = 1;
  int32 longitude = 2;
}

// A feature names something at a given point.
//
// If a feature could not be named, the name is empty.
message Feature {
  // The name of the feature.
  string name = 1;

  // The point where the feature is detected.
  Point location = 2;
}

// A RouteNote is a message sent while at a given point.
message RouteNote {
  // The location from which the message is sent.
  Point location = 1;

  // The message to be sent.
  string message = 2;
}
#+end_src

Grpc supports 2 types of RPCs:
- unary
  - client sends a request
  - server sends a response
- client streaming rpc
  - client sends multiple messages
  - server sends one response
- server streaming rpc
  - client sends one response
  - server sends multiple messages
- bi-directional streaming rpc
  - client and server independently send multiple messages to each other

Now running the proto compiler on this will give you the client and server stubs. You just have to implement the business logic using these stubs. 

Grpc is extensible:
- interceptors
- transports
- auth and security - plugin auth mechanisms
- stats, monitoring, tracing - has promotheseus, zipkin integrations
- service discovery - consul, zookeeper integrations
- supported with proxies - envoy, nginx, linkerd

Grpc has deadline propagation, cancellation propagation

The wire protocol used by grpc is based on HTTP/2 and the specification is well established. 


** Container Storage Interface - https://www.youtube.com/watch?v=ktwY1anKN58
Kubernetes needs storage. So, initially the started with *in-tree plugins* for various storage provisioners, like aws ebs, google persistent disks etc. This was not ideal because:
- the release cycles of the plugins was tied to release of Kubernetes
- the plugin code ran with same access level as the Kubernetes core, so a bug could take down Kubernetes
- the code of the plugins was in the code, and maintaining it was difficult because the aws folks had to get in and make changes and the Kubernetes core team would have to review it for example.
- source code had to be opensourced, regardless of whether the storage vendors wanted that or not


This led to the creation of the *out of tree plugins*. First came flex volumes. These needed binaries to be present on each node(even master), like the CNI binaries and needed root access to the node. This also means, if the master is not accessible (like in many managed Kubernetes services), you can't attach there.

The latest and greatest out of tree implementation is CSI - Container Storage Interface. It is a spec, which is created by folks from not just Kubernetes but other COs like Mesos. Now, any storage vendor who wants to provide storage, has to just implement the CSI spec and his storage solution should be pluggable into the COs.


The In tree volume plugins won't be deprecated because Kubernetes has a very strict deprecation policy. So, the logic will be changed to proxy the calls thru the CSI plugin internally. 


The talk is by Jie Yu, Mesosphere. He is the co-author of the CSI spec.

*** Motivations and background

We have several COs and several storage vendors.

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2018-09-16 16:47:15
[[file:assets/screenshot_2018-09-16_16-47-15.png]]

We wanted a common spec so that the vendors could provide volumes for many COs.

Also, a new standard was created because the existing storage interfaces had one or more of the following issues:
- cli based interface
- lack of idempotency on APIs
- in tree interface
- tightly coupled with an implementation
- too heavyweight 


*** Goals

So, the Goals for CSI were:
- interoperable
- vendor neutral
- focus on specification (CSI committee won't implement anything, only Storage Vendors will)
- control plane only
- keep it simple and boring

First meet on Feb 2017. In Dec 2017, CSI 0.1 released. 
Kubernetes 1.9 added alpha support for CSI.

*** Design Choices

1. in-tree vs out-of-tree
   - in-tree means the volume plugin code will be in the same binary, will be running in the same OS process as CO process
   - possible to have in-tree but out of process, but this gets complicated since we then have to define both north bound API (talk to CO), south bound API (talk to drivers)
   - in out-of-tree implementations, the code lives outside the CO core codebase and the CO calls the plugin via CLI or service interfaces. Eg would be flex volumes.
   - It is possible to be out of tree, but in process via dynamic library loading but it is complicated and won't work if different COs are in different languages.
   - *Decision*: The committee went with out of tree, out of process

2. Service vs. CLI
   - cli - the vendor deploys binaries on hosts, CO invokes the binary with args.
     - this is followed by CNI, flex volumes
   - long running service - vendor deploys services on hosts (via systemd, Kubernetes etc), CO makes requests to the service
   - *Decision*: The committee went with service based because:
     - services are easier to deploy
     - root access is required to install CLI binaries
     - deploying CLI binary dependencies is not easy
     - fuse based backends required long running processes, so why not one more


3. Idempotency
   - this is good for failure recovery

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2018-09-16 17:07:49
[[file:assets/screenshot_2018-09-16_17-07-49.png]]

If CO says create volume A, but the response is lost, the CO will retry and the Storage Plugin will create another volume and the first one will be lost. This is avoided if the API is idempotent. 
For this, the call has to give a id for the volume. 

4. Wire protocol: gRPC

This was chosen because it is language agnostic, easy to write specification, big community with lots of tooling, real production users.
It is also part of the cncf

5. Async vs. Sync APIs

Async would mean when the CO requests a volume, the plugin returns immediately and says okay, I will create the volume and send a message when it is ready. 

Sync would mean the CO blocks

*Decision*: Synchronous APIs
- keep it simple, async is more complex
- async does not solve the long running operation problem - if the operation takes too long, the CO should be able to timeout, this is possible with a sync API
- Plugin implementation can be async, the interface b/w the CO and the plugin has to be synchronous

6. Plugin packaging and deployment
How should the plugin be packaged and deployed? 

*Decision*: Don't dictate
The spec does not dictate that. The only requirement is that it should provide a grpc endpoint over unix socket for now.

So, possible options for deployment:
- container deployed by CO (eg DaemonSet in Kubernetes)
- systemd services deployed by cluster admin
- can have running services someplace else, the CO connects via grpc (so you can have a company that provides these endpoints)

7. Controller and Node services
  - Identified 2 sets of control plane operations that have different characteristics - Node and Controller services.
  - Node services are the services that have to run on the node that is using the volume
    - OS mount/unmount, iSCSI initiator
  - Controller services can be run on any node
    - volume attach/detach (eg EBS), volume creation/deletion
  - *Decision*: Created 2 above mentioned sets of APIs

There are several options for deploying the node services and controller services.
   - Option 1: Split Controller and Node services

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2018-09-16 17:03:45
[[file:assets/screenshot_2018-09-16_17-03-45.png]]

Here, we have the Node Service deployed on all the nodes, and there is a controller service running "somewhere" that the master can call. 

   - Option 2: Headless

Some COs don't have the master node, everything is just a node. In this case, you can bundle both the node and the controller service on a single container and the CO will talk to it. 

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2018-09-16 17:05:43
[[file:assets/screenshot_2018-09-16_17-05-43.png]]
  


*** Spec Overview

There are 3 core grpc services
1. Identity
Gives basic information about the plugin, like the GetPluginInfo, GetPluginCapabilities, Probe (to see if the plugin is healthy or not etc). 

2. Controller


- ~CreateVolume~
- ~DeleteVolume~
- ~ControllerPublishVolume~ - make the volume available on the node (can be just "attach" on the node, (not mount, attach))
- ~ControllerUnpublishVolume~
- ~ValidateVolumeCapabilities~
- ~ListVolumes~
- ~GetCapacity~
- ~ControllerGetCapabilities~

Many of the calls are optional. 


3. Node
This service has to run on the node where the volume will be used

- ~NodeStageVolume~ - this should be called only once for a volume on a given node (eg ~mount~)
- ~NodeUnstageVolume~
- ~NodePublishVolume~
- ~NodeUnpublishVolume~
- ~NodeGetId~
- ~NodeGetCapabilities~


#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2018-09-16 17:54:10
[[file:assets/screenshot_2018-09-16_17-54-10.png]]
The lifecycle for the volume. 
Note, first the ControllerPublishVolume is called to make the volume "ready". Then the NodePublishVolume is called to "mount" etc

If you want to write a plugin for GCE PD say,
- need both controller and node services
- create the persistent disk with CreateVolume (use the google cloud apis underneath)
- attach the disk in the ControllerPublishVolume
- format and mount the volume in the NodeStageVolume
- perform a bind mount in the NodePublishVolume

(Reference implementation in: github.com/googlecloudplatform/compute-persistent-disk-csi-driver)

Can create a plugin for LVM - logical volume manager etc

*** CO Integrations

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2018-09-16 17:58:56
[[file:assets/screenshot_2018-09-16_17-58-56.png]]


We have 2 types of pods (each with a sidecar provided by the Kubernetes team. The side car sits b/w the volume plugin and the apiserver. It understands the CSI spec and the apiserver also. It translates commands between the two)
- controller pod - running the controller service of the plugin
- node pod - running the node service of the plugin

*** Governance model
- Inclusive and open
- independent of any single CO
- try to avoid a storage vendor war

*** Future
- topology aware
  - the CO needs to know whether a volume can be used within a given zone or not
- snapshot support
- volume resizing
- plugin registration (to tell the CO here the unix socket for the plugin is)
  - Kubernetes already has it's own spec for this - called device plugin
- smoke test suite

** External Provisioner - https://github.com/kubernetes-csi/external-provisioner

Ref: https://kubernetes-csi.github.io/docs/CSI-Kubernetes.html

Recall we had sidecar containers running along with the CSI volumes which are there to translate between the CSI speak that the volume speaks and the apiserver.

#+ATTR_ORG: :width 400
#+ATTR_ORG: :height 400
#+DOWNLOADED: /tmp/screenshot.png @ 2018-09-16 19:28:37
[[file:assets/screenshot_2018-09-16_19-28-37.png]]

Here, we see that on a node, there are 2 containers - the side car container, and the CSI driver container. There are 3 sidecars that are needed to manage Kubernetes events and make appropriate calls to the CSI driver.

They are: external attacher, external provisioner, driver registrar

*** External Attacher
This implements the ~Controller~ services. It watches the Kubernetes ~VolumeAttachment~ objects and triggers CSI ~ControllerPublish~ and ~ControllerUnpublish~ operations against the driver endpoint.

*** External Provisioner
It looks out for ~PersistentVolumeClaim~ objects and triggers the ~CreateVolume~ and ~DeleteVolume~ operations against the driver endpoint. 

*** Driver Registrar
It is a sidecar container that registers the CSI driver with the kubelet and adds the drivers custom NodeId to a label on the Kubernetes Node API object.


The kubelet runs on every node and is responsible for making the CSI calls ~NodePublish~ and ~NodeUnpublish~. 
Very important document - https://github.com/kubernetes/community/blob/master/contributors/design-proposals/storage/container-storage-interface.md


The design docs say that there will be an in-tree csi plugin:
#+BEGIN_QUOTE
To support CSI Compliant Volume plugins, a new in-tree CSI Volume plugin will be introduced in Kubernetes. This new volume plugin will be the mechanism by which Kubernetes users (application developers and cluster admins) interact with external CSI volume drivers.
#+END_QUOTE

However, 

#+BEGIN_QUOTE
Provision/delete and attach/detach must be handled by some external component that monitors the Kubernetes API on behalf of a CSI volume driver and invokes the appropriate CSI RPCs against it.
#+END_QUOTE

So, we have external provisioner and external attacher respectively. 

** Mount Propagation
ref: https://medium.com/kokster/kubernetes-mount-propagation-5306c36a4a2d
Mount Propagation, was introduced in Kubernetes v1.8

The filesystem that we browse is called the VFS - virtual file system. The File system (or more accurately the VFS) hides the complexity of writing to the actual physical location on the disk. 

The VFS has this struct in mount.h

#+begin_src c
struct vfsmount {
        struct list_head mnt_hash;
        struct vfsmount *mnt_parent;    /* fs we are mounted on */
        struct dentry *mnt_mountpoint;  /* dentry of mountpoint */
        struct dentry *mnt_root;        /* root of the mounted
                                           tree*/
        struct super_block *mnt_sb;     /* pointer to superblock */
        struct list_head mnt_mounts;    /* list of children,
                                           anchored here */
        struct list_head mnt_child;     /* and going through their
                                           mnt_child */
        atomic_t mnt_count;
        int mnt_flags;
        char *mnt_devname;              /* Name of device e.g. 
                                           /dev/dsk/hda1 */
        struct list_head mnt_list;
};
#+end_src

Look at ~dentry~ first

The ~dentry~ struct is used to represent "inode, file name, parent directory, other files in same directory (siblings), and sub-directory"


#+begin_src c
struct dentry {
        struct inode *d_inode;
        struct dentry *d_parent;
        struct qstr d_name;
        struct list_head d_subdirs;  /* sub-directories/files     */
        struct list_head d_child;    /* sibling directories/files */
        ...
}
#+end_src
In the ~vfsmount~ struct from earlier, ~mnt_mountpoint~ and ~mnt_root~ are of type ~dentry~. 
When the OS mounts, a ~vfsmount~ is created with ~mnt_mountpoint~ set to ~/~

#+BEGIN_QUOTE
This operation of creating vfsmount entry for a specific dentry is what is commonly referred to as Mounting. 
A tree of dentry structs form the file-system.

                          root
                         /    \
                       tmp    usr
                       /
                      m1
                     /  \
                   tmp  usr 
                   /
                  m1
#+END_QUOTE

~dentry~ struct is used when the ~cd~ is invoked

So, mount is just the creating of the ~vfsmount~ and ~dentry~ data structures. Since it is only that, all these are valid:
- mount a device at one path - the simplest case
- mount a device multiple times at different paths - different vfsmount, different dentry
- mount a device multiple times at same paths - different vfsmount, same dentry
- mount a directory at another path

