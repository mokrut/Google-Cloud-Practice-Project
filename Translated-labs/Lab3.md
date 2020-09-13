# Lab 1 : 
## Google Cloud Fundamentals: Getting Started with GKE

### Overview
In this lab, you create a Google Kubernetes Engine cluster containing several containers, each containing a web server. You place a load balancer in front of the cluster and view its contents.

### Objectives
In this lab, you learn how to perform the following tasks:
+ Provision a Kubernetes cluster using Kubernetes Engine.
+ Deploy and manage Docker containers using kubectl.

To accomplish the above objectives you have to sign into your [google cloud console](https://console.cloud.google.com)

##### **Warning** You might incure costs by following these instructions. 

> **Note:** When required, always set your default project and compute zone in cloud Shell replacing *PROJRCT_ID* with your actual project ID and *COMPUTER/ZONE* with your actual zone when requied in the commands below:

`gcloud config set project PROJECTID`

`gcloud config set compute/zone COMPUTER/ZONE`

> - **All the tasks are carried out in the Cloud Shell unless told to use another tool.**

### Task 1: Confirm that needed APIs are enabled
- In GCP console, on the top right toolbar, click the **Open Cloud Shell** button.
- Click **Continue** if prompted.

#### Confirm if the Kubernetes Engine API and Container Registry API are enabled using .

Use the command below to confirm the available APIs.

`gcloud services list --available`

- If you don't see Kubernetes Engine API and Container Registry API listed, then you have to activate them using the commands below replacing *SERVICE_NAME* with the respective service that you want to activate. 

`gcloud services enable SERVICE_NAME`

### Task 2: Start a Kubernetes Engine cluster.

- For convenience, place the your zone into an environment variable called MY_ZONE.Type this command replacing *COMPUTER/ZONE* with your actual zone.

`export MY_ZONE=COMPUTER/ZONE`.

#### Start a Kubernetes cluster. 
Start a Kubernetes cluster managed by Kubernetes Engine. Name the cluster webfrontend and configure it to run 2 nodes: 
Your command should look like this 

`gcloud container clusters create webfrontend --zone $MY_ZONE --num-nodes 2`.

> It takes several minutes to create a cluster as Kubernetes Engine provisions virtual machines for you.
- After the cluster is created, check your installed version of Kubernetes using the kubectl version command:

`kubectl version`

- The gcloud container clusters create command automatically authenticated kubectl for you.
- View your running nodes using 
> `kubectl get pods` 

> Your Kubernetes cluster is now ready for use.

### Task 3: Run and deploy a container
- Launch a single instance of the nginx container. (Nginx is a popular web server). Use:

`kubectl create deploy nginx --image=nginx:latest`

> In Kubernetes, all containers run in pods. kubectl created command a deployment consisting of a single pod containing the nginx container. A Kubernetes deployment keeps a given number of pods up and running even in the event of failures among the nodes on which they run.

**Note:** If you see any deprecation warning about future version you can simply ignore it for now and can proceed further.

- To View the pod running the nginx container: Run

`kubectl get pods`

- Expose the nginx container to the Internet using: 

`kubectl expose deployment nginx --port 80 --type LoadBalancer`

> Kubernetes created a service and an external load balancer with a public IP address attached to it. The IP address remains the same for the life of the service. Any network traffic to that public IP address is routed to pods behind the service: in this case, the nginx pod.

- View the new service by running:

`kubectl get services`

> You can use the displayed external IP address to test and contact the nginx container remotely. It may take a few seconds before the External-IP field is populated for your service. This is normal. Just re-run the kubectl get services command every few seconds until the field is populated.

- Open a new web browser tab and paste your cluster's external IP address into the address bar. The default home page of the Nginx browser is displayed.

- Scale up the number of pods running on your service to 3:

`kubectl scale deployment nginx --replicas 3`

Scaling up a deployment is useful when you want to increase available resources for an application that is becoming more popular.

- Confirm that Kubernetes has updated the number of pods:

`kubectl get pods`

- Confirm that your external IP address has not changed:

`kubectl get services`

- Return to the web browser tab in which you viewed your cluster's external IP address. Refresh the page to confirm that the nginx web server is still responding.

Exit and disable or delete all the resources that you wont need to avoid charges.
