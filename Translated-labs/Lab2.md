# Lab 2 : 
## AK8S-03 Creating a GKE Cluster via GCP Console

### Overview
In this lab, you build GKE clusters and deploy a sample Pod.
### Objectives
You learn how to perform the following tasks:
- Use the Cloud Shell to build and manipulate GKE clusters
- Use the Cloud Shell to deploy a Pod
- Use the Cloud Shell to examine the cluster and Pods

To accomplish the above objectives you have to sign into your [google cloud console](https://console.cloud.google.com)

##### **Warning** You might incure costs by following these instructions. 

> **Note:** When required, always set your default project and compute zone in cloud Shell replacing *PROJRCT_ID* with your actual project ID and *COMPUTER/ZONE* with your actual zone when requied in the commands below:

`gcloud config set project PROJECTID`

`gcloud config set compute/zone COMPUTER/ZONE`

> - **All the tasks are carried out in the Cloud Shell unless told to use another tool.**

### Task 1: Start Cloud shell and confirm that needed APIs are enabled

- In GCP console, on the top right toolbar, click the **Open Cloud Shell** button.
- Click **Continue** if prompted.

#### Confirm if the Kubernetes Engine API and Container Registry API are enabled using.

Use the command below to confirm the available APIs.

`gcloud services list --available`

- If you don't see Kubernetes Engine API and Container Registry API listed, then you have to activate them using the commands below replacing *SERVICE_NAME* with the respective service that you want to activate. 

`gcloud services enable SERVICE_NAME`

- Run the following command, replacing project-id with your project ID if you didn't do it earlier to Set your default project.

`gcloud config set project PROJECTID`

- Run the following command, replacing compute-zone with your compute zone such as us-Central1-a if you didn't do it earlier.

`gcloud config set compute/zone COMPUTER/ZONE`

### Task 1. Deploy GKE clusters
- In the Cloud shell create a gke cluster replacing CLUSTER-NAME with **standard-cluster-1** and COMPUTE-ZONE with **us-central1-a**
 
 `gcloud container clusters create CLUSTER-NAME --zone COMPUTE-ZONE`

The cluster begins provisioning. In a few minutes, provisioning is complete

- You can view your cluster by replacing CLUSTER_NAME with **standard-cluster-1** 

`cloud container clusters describe CLUSTER_NAME`

 - Get authentication details of the cluster. This allows kubectl to manage the cluster.

`gcloud container clusters get-credentials cluster-name`

### Task 3. Modify GKE clusters
- We created a standard cluster that has 3 nodes. We are going to add 1 more node to make 4 nodes By replacing CLUSTER_NAME with **standard-cluster-1** and NUM_NODES with **4**. This takes some time.

``gcloud container clusters resize CLUSTER_NAME --num-nodes NUM_NODES`

### Task 4. Deploy a sample workload
- In this task, you will deploy a Pod running the nginx web server as a sample workload using the command below:

`kubectl create deploy nginx --image=nginx:1.17.10`

- View details about workloads in the GCP Console by typing:

`kubectl get pods`

> You'll see the details of the pods running the nginix server 

Exit and disable or delete all the resources that you wont need to avoid charges.