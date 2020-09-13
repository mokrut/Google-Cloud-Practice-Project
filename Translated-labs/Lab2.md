# Lab 2 : 
## AK8S-03 Creating a GKE Cluster via GCP Console

### Overview
In this lab, you build GKE clusters and deploy a sample Pod.
### Objectives
You learn how to perform the following tasks:
+ Use the Cloud Shell to build and manipulate GKE clusters
+ Use the Cloud Shell to deploy a Pod
+ Use the Cloud Shell to examine the cluster and Pods

### Sign in to the Google Cloud Platform (GCP) Console
To meet the above objectives you have to sign into your [google cloud console](https://console.cloud.google.com)

### Task 1: Start Cloud shell and confirm that needed APIs are enabled
1. Make a note of the name of your GCP project which is shown in the top bar of the Google Cloud Platform Console. 
It will be of the form `qwiklabs-gcp-` followed by hexadecimal numbers.
2. In GCP console, on the top right toolbar, click the **Open Cloud Shell** button.
3. Click **Continue**.
4. Confirm if the Kubernetes Engine API and Container Registry API are enabled using 
> `gcloud services list --available`.
5. If you don't see the API listed, that means you haven't been granted access to enable the API. Using the API name from the previous step, enable it with 
> `gcloud services enable SERVICE_NAME`
6. Setting a default project. Run the following command, replacing project-id with your project ID
>`gcloud config set project project-id`
7. Setting a default compute zone Run the following command, replacing compute-zone with your compute zone asigned by quicklabs, such as us-Central1-a:
> `gcloud config set compute/zone compute-zone`
### Task 2. Deploy GKE clusters
1. In the Cloud shell create a gke cluster replacing CLUSTER-NAME with **standard-cluster-1** and COMPUTE-ZONE with **us-central1-a**
> `gcloud container clusters create CLUSTER-NAME --zone COMPUTE-ZONE`

The cluster begins provisioning. In a few minutes, provisioning is complete

2. You can view your cluster by replacing CLUSTER_NAME with **standard-cluster-1** 
> `cloud container clusters describe CLUSTER_NAME`
3. Get authentication details of the cluster. This allows kubectl to manage the cluster
> `gcloud container clusters get-credentials cluster-name`
### Task 3. Modify GKE clusters
1. We created a standard cluster that has 3 nodes. We are going to add 1 more node to make 4 nodes By replacing CLUSTER_NAME with **standard-cluster-1** and NUM_NODES with **4**. This takes some time.
>``gcloud container clusters resize CLUSTER_NAME --num-nodes NUM_NODES`## Congratulations!

### Task 4. Deploy a sample workload
1. In this task, using the GCP console you will deploy a Pod running the nginx web server as a sample workload. 

`kubectl create deploy nginx --image=nginx:1.17.10`

2. View details about workloads in the GCP Console
>`kubectl get pods`
> You'll see the details of the pods running the nginix server 

Exit and disable all the resources that you wont need to avoid charges.