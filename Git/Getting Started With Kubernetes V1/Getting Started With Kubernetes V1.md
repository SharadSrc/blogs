### Overview


A few years back businesses used to deploy applications by installing them on a server using the operating system's package manager, and then virtual machines were usually used to scale the application. The downside of doing it this way is that the executables, the configuration files, the underlying operating system and the life cycle of all these components are entangled with each other, in addition to the fact that the virtual machines are memory-intensive, and are very costly to sustain. To solve these drawbacks, the container concept was designed.

Containerization of any software allows system developers or dev-ops engineers to rapidly deploy applications to any cloud infrastructure in the world and scale them according to their application user load, without any downtime. With container orchestration. DevOps team now can manage cloud-native and distributed applications lifecycle easily and can also bundle legacy applications for greater portability and efficient deployment.

Of all the container creation systems, the best known is Docker, but we have others like ContainerD or LXC etc.


Just as there are numerous ways to assemble containers, there are also different orchestrators as well in the industries as well:

- **Kubernetes**: It is a container orchestration system developed by Google from its internal experiences and released in mid-2014. It is currently the most widely used system of all and has deployments in each of the major public cloud providers. Later it was donated to the open-source CNCF foundation by google.
- **Docker Swarm:** is the system developed by Docker to orchestrate its containers. Although it is not commonly used as Kubernetes in big companies due to the lack of open-source capabilities, it is still popular in the market despite lacking some core functionalities that do have its competition. E.g. Docker swarm is lightweight and easy to start in comparison to Kubernetes.
- **Nomad**: is the system used to orchestrate workloads, either in containers or virtual machines, developed by Hashicorp.


For this blog, we will discuss the most heavily used orchestrator i.e. Kubernetes orchestrator.









### What is Kubernetes?

Kubernetes, the de facto technology for container management and orchestration, is known to be complex to configure and implement. However, once it's up and running, it's highly adaptable, customizable and extensible to process the need of diverse uses case of software & IT industries.


It has a large and rapidly growing ecosystem. Support, tools, and services for Kubernetes are widely available and very well documented.


Kubernetes offers an application with the following features:

- High availability.
- Scalability.
- Failover healing.
- Application Isolation.
- Distributed Computation
- Automatic Deployment and many more.


### Terminology
Before we start technical deep dive, let's understand the common vocabulary used while referring to the Kubernetes system:

- **Kubernetes**: Kubernetes (also called Kube, or K8s ) represent the entire orchestration system, that automates various manual steps like deployment, scaling etc.
- **Kubectl**: (also called Kube control ) is the CLI application used to communicate with Kubernetes and allows its configuration and management
- **Node**: any server within a Kubernetes cluster
- **Kubelet**: it is a container that acts as a system agent, and that is executed in each one of the nodes.
- **Control Plane**: (also called master control layer)It is the powerhouse of Kubernetes. It acts as a master node and regulates others. It includes the various components like the API server, the scheduler, the controller manager, etc.

###






###
### **Basic Kubernetes Architecture**
First, it's helpful to understand the foundation of a Kubernetes system, which revolves around four important concepts: nodes, pods, deployments, and services. The servers that run the containers, and the applications within them, are called nodes. A master server controls the management and configuration of these node servers. 

The master server acts as the control plane for the Kubernetes clusters. The main purpose of the various components in the control plane is to keep track of all Kubernetes objects and continually manage the desired state of the cluster by automatically responding to changes.



![](img.001.png)

Reference: kubernetes.io


Let's understand each component in detail:













#### **Pod** 
It is the basic deployment unit. All containers are always in pods. In simpler terms, they are an abstraction of containers so they can have one or more containers inside them.

![](img.002.png)



#### **Replica Sets**
ReplicaSet is a Kubernetes feature that ensures that a number of replicas of a given pod are always executed. They assure us that our pods are always available. Also, if some of the pods fail then Kubernetes try to heal else it will spin up more replicas of the application so service will never be down. It offers us the following features:

- 24*7 Service availability
- Low latency based on geographical deployment
- High Availability 

![](img.003.png)
#### **Deployment**
This component is responsible for the deployment of the pods, that is, it creates them with the assigned characteristics. It is a type of replication for stateless pods or pods that do not require data consistency. In laymen's terms,


It allows us to define different functions:

- Control the number of replicas
- Scalable pods
- Updates
- Automated deployments
- Rollback to previous versions
#### **Service**
It is a component that exposes access to a pod. Normally an application does not directly interact with a pod, because it can be destroyed at any time.

Hence, When a new service is exposed, it is assigned a new virtual internal IP (Cluster-IP) that allows connections from other pods within the cluster. We can also enable access from the outside, a random port is opened that allows accessing the IP of the cluster and that port to access the set of pods. If we have more than one pod the access will be done following a round-robin policy.
#### **Other Important Components:**
- **Ingress**: It is a service router, it works as an API-Gateway for incoming traffic to the application.
- **Persistent Volume**: It is a component that allows you to save the information, to later retrieve it. Because pods are deadly, when they disappear and are redeployed, the old information is gone. Therefore, this component allows information to be recovered, avoiding information loss.
- **Config Map**: This contains the information of the URL of the database, in this way a service knows which database to consult.
- **Secret:** Contains the authentication information of a database, such as a username and a password, both data must be encrypted in Base64 to keep the information safe.
- **Namespace**: All these components described above are configurable files with a .yaml extension, these components can be combined within another file called a namespace. This allows you to better organize the components that make up the application.

Let's start with the key steps and Kubernetes commands for a basic Kubernetes setup and deployment.


### Kubernetes Set-up Steps
This section explains how to install and configure Kubernetes on Mac Operating System.

The goal here is to make a stand-alone installation for the developer. We will see how to install Minikube to use Kubernetes on Mac OS.

The different steps to install local Kubernetes are as follows:

- Install **Homebrew**
- Install **Docker**
- Install **kubectl**
- Install and start **Minikube**
- Access the Kubernetes **Dashboard**

#### **Install Homebrew**
To get started, Initially, a package manager for macOS needs to be installed e.g. Homebrew. This can be installed via the below command:

```
/usr/bin/ruby -e "$(curl -fsSL <https://raw.githubusercontent.com/Homebrew/install/master/install>)"

```

Verify that the installation was successful by running the following command:


```
brew -v
```

#### **Install Docker or HyperKit**
A hypervisor allows you to virtualize OS on your machine. On macOS, the hypervisors supported by Minikube are[ ](https://www.virtualbox.org/wiki/Downloads)[**VirtualBox**](https://www.virtualbox.org/wiki/Downloads), [**VMware Fusion**](https://www.vmware.com/products/fusion), **Docker Desktop** and [**HyperKit](https://github.com/moby/hyperkit)**.**

In this tutorial, we have selected to use Docker.

The installation is done very simply based on an M1 or Intel-based chip using standard documentation below:

[Install Docker Desktop on Mac](https://docs.docker.com/desktop/mac/install/)

Alternatively,  you can use the brew package manager to install it. The following command will setup “hyperkit” in the system.

```
brew install hyperkit
```

#### **Install kubectl**

A proxy client allows us to control various components of the Kubernetes cluster. The most commonly used tool is kubectl. It is a command-line tool for interacting with the Kubernetes API, used to manage most Kubernetes objects, such as pods, services, namespaces, etc. It also allows you to know the general status of the cluster.

The installation is done with Homebrew:

```
brew install kubernetes-cli

```

After successful installation you can check the below command:


```
kubectl version
```
#### **Install Minikube**

Minikube is a tool for running a Kubernetes cluster with a single node in a virtual machine: it facilitates local execution with support for all Kubernetes features. The following command will install minikube via brew



```
brew cask install minikube

```
It will show a similar output to the console

![](img.004.png)

Once installed, you must use the following command to start the famous cluster:

```

minikube start

````
The console will show the following output.

![](img.005.png)

From now on, Minikube gives you access to a local cluster, with which kubectl will interact.

Additionally, you can start the minikube cluster in verbose mode using the following command. This will show the detailed logs on the screen 

```
minikube start --alsologtostderr
```

After completing the above process, you can check the deployment status of Minikube.



```

minikube status

```


#### **Access Kubernetes Dashboard**
Minikube provides a dedicated command for accessing the Kubernetes dashboard running within the Minikube cluster.

Execute the command below

```
minikube dashboard
```

The above command will open the Kubernetes dashboard. Here we can see all the components of the Kubernetes cluster e.g. pods, jobs, deployment etc.

Below is a screenshot of the Kubernetes dashboard running locally in your browser.

![](img.006.png)

Additionally, the Minikube service can be stopped with the below command as well:

```
minikube stop
```
### Basic Commands 
We can use the following commands using kubectl to get details from Kubernetes.

**kubectl config view:**  To view kubeconfig settings
**kubectl cluster-info:** To view current cluster-info
**kubectl version:** To view the Kubernetes version installed
**kubectl get services:** To view all the services in the namespace
**kubectl get deployments:** List one or more deployments
**kubectl get pods --all-namespaces:** To view the list of all pods deployed in the Kubernetes cluster,    use the below command
**kubectl get services:** List all the services and their state 
**kubectl logs <pod_name> :** Print the logs for a pod
**kubectl describe pod <pod_name> :** Display the detailed state of a pods
**kubectl get pods -o wide:** View a detailed expanded  list of all pods

### Conclusion
Kubernetes has revolutionized the software/IT industry by making packaged applications readily available and making it highly scalable. Kubernetes can address some of the most standard issues of the software world like scalability, user performance, increasing maintenance cost and service failures. 


