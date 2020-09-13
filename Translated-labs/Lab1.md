# Lab 1 : 
## VPC Networking

### Overview
In this lab, you create an auto mode VPC network with firewall rules and two VM instances. Then, you convert the auto mode network to a custom mode network and create other custom mode networks. You also test connectivity across networks.
### Objectives
In this lab, you learn how to;
- Explore the default VPC network
- Create an auto mode network with firewall rules
- Convert an auto mode network to a custom mode network
- Create custom mode VPC networks with firewall rules
- Create VM instances using Compute Engine
- Explore the connectivity for VM instances across VPC networks

To accomplish the above objectives you have to sign into your [google cloud console](https://console.cloud.google.com)

##### **Warning** You might incure costs by following these instructions. 

> **Note:** When required, always set your default project and compute zone in cloud Shell replacing *PROJRCT_ID* with your actual project ID and *COMPUTER/ZONE* with your actual zone when requied in the commands below:

`gcloud config set project PROJECTID`

`gcloud config set compute/zone COMPUTER/ZONE`

> - **All the tasks are carried out in the Cloud Shell unless told to use another tool.**

### Task 1: Explore the default network
- In GCP console, on the top right toolbar, click the **Open Cloud Shell** button,  Click **Continue** if prompted.

- Set the your default project by replacing *PROJECTID* with actual Project ID in the command;

`gcloud config set project PROJECTID`

> Each Google Cloud project has a default network with subnets, routes, and firewall rules.

#### View the subnets.

The default network has a subnet in each Google Cloud region. To view subnets run the following command:

`gcloud compute networks subnets list --sort-by=NETWORK`

 > **Note** `--sort-by=NETWORK` sorts subnets by network.

#### View the routes.

Routes tell VM instances and the VPC networks how to send traffic from an instance to a destination, either inside the network or outside Google Cloud. To view routes run;

`gcloud compute routes list --sort-by=NETWORK` 

#### View the firewall rules.

Firewall rules allow you to control which packets are allowed to travel to which destinations. To view the firewall rules run;

 `gcloud compute firewall-rules list --sort-by=NETWORK`

#### Delete the default Firewall Rules. 
To delete Firewall rules run the following command:

```
gcloud compute firewall-rules delete \
-- default-allow-internal \
-- default-allow-rdp \
-- default-allow-ssh \
-- default-allow-icmp 
```

Type Y when prompted and Enter.

#### Delete the Default network. 
`gcloud compute networks delete default`

Type Y when prompted and Enter

> Deleting the default network deletes all its routes. Comfirm by runing,

`gcloud compute routes list --sort-by=NETWORK` 

#### Try to create a vm. 
This is to Verify that you cannot create a VM instance without a VPC network.
We shall create an **f1-micro** vm named **nettest-vm** in the **us-central1-c** zone

```
gcloud compute instances create nettest-vm \
--zone=us-central1-c \
--machine-type=f1-micro
```

 As expected, you get an *Invalid value for field 'resource.networkInterfaces[0].network'* error and cannot create a VM instance without a VPC network!

### Task 2. Create an auto mode network
#### Create an auto mode VPC network with a name **mynetwork**, subnet creations mode **Automatic** with all availabe default firewall rules.

`gcloud compute networks create mynetwork --subnet-mode=auto`

#### Create a VM instance in us-central1
Create a VM instance in the us-central1 region specifying the following:

Property	| Value (type value or select option as specified)
--------------|--------------------
Name	| mynet-us-vm
Zone	| us-central1-c
Machine type	| n1-standard-1 (1 vCPU, 3.75 GB memory)
Network | mynetwork

Run the following command to create the vm:

```
gcloud compute instances create mynet-us-vm \
--zone=us-central1-c \
--machine-type=n1-standard-1 \
--network=mynetwork
```

Verify that the Internal IP for the new instance was assigned from the IP address range for the default subnet in us-central1 (10.128.0.0/20).

#### Create a VM instance in europe-west1.
Create a VM instance in the us-central1 region specifying the following:

Property	| Value (type value or select option as specified)
-----------|------------------------
Name	| mynet-eu-vm
Zone	| europe-west1-c
Machine type	| n1-standard-1 (1 vCPU, 3.75 GB memory)
Network | mynetwork
Run the following command to create the vm;

```
gcloud compute instances create mynet-eu-vm \
--zone=europe-west1-c \
--machine-type=n1-standard-1 \
--network=mynetwork
```
 
 Verify that the Internal IP for the new instance was assigned from the IP address range for the subnet in europe-west1 (10.132.0.0/20).

#### Verify connectivity for the VM instances created.
 SSH from mynet-us-vm by running'
 
 `gcloud compute ssh mynet-us-vm --zone=us-central1-c`
 > You'll get a request to create a home directory and  to generate ssh keys, accept them.
 
 + To test connectivity to mynet-eu-vm's internal IP, run the following command,

`ping -c 3 10.128.0.2`

You can ping mynet-eu-vm's internal IP because of the allow-internal firewall rule.

+ Repeat the same test by running the following:

`ping -c 3 mynet-eu-vm`

 You can ping mynet-eu-vm by its name because VPC networks have an internal DNS service that allows you to address instances by their DNS

+ To test connectivity to mynet-eu-vm's external IP, run the following command:

 `ping -c 3 <Enter mynet-eu-vm's external IP here>`

#### Convert **mynetwork** to a custom mode network
The auto mode network worked great so far, but you have been asked to convert it to a custom mode network so that new subnets aren't automatically created as new regions become available. Convert the the network by running the following command: 
`gcloud computer networks 

`gcloud compute networks update mynetwork --switch-to-custom-subnet-mode`
> Type **y** then press enter to accept change.

### Task 3. Create custom mode networks
You have been tasked to create two additional custom networks, managementnet and privatenet, along with firewall rules to allow SSH, ICMP, and RDP ingress traffic and VM instances
#### Create the Management Network. 
Create the managementnet network with a name **managementnet**and  creations mode **custom**.

Specifiy the follwing settings for the subnets:

Property	| Value (type value or select option as specified)
-------------------|-----------------------------
Name	| managementsubnet-us
Region	| us-central1
IP address range	| 10.130.0.0/20

- First we create the Network by running.

`gcloud compute networks create managementnet --subnet-mode=custom`

- Then we create the subnet
```
gcloud compute networks subnets create managementsubnet-us \
--network=managementnet \
--region=us-central1 \
--range=10.130.0.0/20
```

### Create the Private Network.
Create the privatenet network with a name **privatenet** connecting two (2) subnets;
1. **privatesubnet-us** in the **us-central1** region with subnet creations mode **custom** and IP address range  **172.16.0.0/24** 
2. **privatesubnet-eu** in the **europe-west1** region with subnet creations mode **custom** and IP address rannge **172.20.0.0/20**.

First create the Network by running.

`gcloud compute networks create privatenet --subnet-mode=custom`

Then create the **privatesubnet-us** subnet with IP address range **172.16.0.0/24**. in the **us-central1** region by running;

```
gcloud compute networks subnets create privatesubnet-us \
--network=privatenet \
--region=us-central1 \
--range=172.16.0.0/24
```

Finally create the **privatesubnet-eu** subnet with IP address Range **172.20.0.0/20** in the **europe-west1** region

```
gcloud compute networks subnets create privatesubnet-eu \
--network=privatenet \
--region=europe-west1 \
--range=172.20.0.0/20
```

List the available VPC networks, run the following command:

`gcloud compute networks list`

List the available VPC subnets (sorted by VPC network), run the following command:

`gcloud compute networks subnets list --sortby=NETWORK`

> The managementnet and privatenet networks only have the subnets that you created because they are custom mode networks. mynetwork is also a custom mode network, but it started out as an auto mode network, resulting in subnets in each region.

### Create firewall rules for management network.
Specify the following,
Property	| Value (type value or select option as specified)
--------------|-------------------------
Name	| managementnet-allow-icmp-ssh-rdp
Network	| managementnet
Targets	| All instances in the network
Source | filter	IP Ranges
Source | IP ranges	0.0.0.0/0
Protocols and ports	| Specified protocols and ports

To create the firewall rule, run the following:
```
gcloud compute firewall-rules create managementnet-allow-icmp-ssh-rdp \
--direction=INGRESS \
--priority=1000 \
--network=managementnet \
--action=ALLOW \
--rules=icmp,tcp:22,tcp:3389 \
--source-ranges=0.0.0.0/0
```

### Create the firewall rules for private network.
To create the privatenet-allow-icmp-ssh-rdp firewall rule, run the following command:

```
gcloud compute firewall-rules create privatenet-allow-icmp-ssh-rdp \
--direction=INGRESS \
--priority=1000 \
--network=privatenet \
--action=ALLOW \
--rules=icmp,tcp:22,tcp:3389 \
--source-ranges=0.0.0.0/0
```

To list all the firewall rules (sorted by VPC network), run the following command:

`gcloud compute firewall-rules list --sort-by=NETWORK`

### Create the managementnet-us-vm instance
 Create an **f1-micro (1 vCPU, 614 MB memory)** vm named **managementnet-us-vm** in the **us-central1-c** zone

 ```
 gcloud compute instances create management-us-vm \
 --zone=us-central1-c \
 --machine-type=f1-micro \
 --subnet=managementsubnet-us
 ```

### Create the privatenet-us-vm instance 
Create an **f1-micro (1 vCPU, 614 MB memory)** vm named **private-us-vm** in the **us-central1-c** zone

```
gcloud compute instances create privatenet-us-vm \
--zone=us-central1-c \
--machine-type=f1-micro \
--subnet=privatesubnet-us
```

List all the VM instances (sorted by zone), run the following command:

`gcloud compute instances list --sort-by=ZONE`

### Task 4. Explore the connectivity across networks
#### Ping the external IP addresses
SSH from mynet-us-vm by running'
 
  `gcloud compute ssh --zone=us-central1-c mynet-us-vm`

 To test connectivity to mynet-eu-vm's external IP, run the following command, replacing mynet-eu-vm's external IP:

 `ping -c 3 <Enter mynet-eu-vm's external IP here>`

 > This should work!

 To test connectivity to managementnet-us-vm's external IP, run the following command, replacing managementnet-us-vm's external IP:

 `ping -c 3 <Enter managementnet-us-vm's external IP here>`

 > This should work!

 To test connectivity to privatenet-us-vm's external IP, run the following command, replacing privatenet-us-vm's external IP:

 `ping -c 3 <Enter privatenet-us-vm's external IP here>`
 > This should work!

> You can ping the external IP address of all VM instances, even though they are in either a different zone or VPC network. This confirms that public access to those instances is only controlled by the ICMP firewall rules that you established earlier.
### Ping the internal IP addresses
To test connectivity to mynet-eu-vm's internal IP, run the following command, replacing mynet-eu-vm's internal IP:

`ping -c 3 10.132.0.2`
> You can ping the internal IP address of mynet-eu-vm because it is on the same VPC network as the source of the ping (mynet-us-vm), even though both VM instances are in separate zones, regions, and continents!

To test connectivity to managementnet-us-vm's internal IP, run the following command, replacing managementnet-us-vm's internal IP:

`ping -c 3 10.130.0.2`
> This should not work, as indicated by a 100% packet loss!gcloud compute ssh --zone=us-central1-c mynet-us-vm

To test connectivity to privatenet-us-vm's internal IP, run the following command, replacing privatenet-us-vm's internal IP:

`ping -c 3 172.16.0.2`
> This should not work either, as indicated by a 100% packet loss! You cannot ping the internal IP address of managementnet-us-vm and privatenet-us-vm because they are in separate VPC networks from the source of the ping (mynet-us-vm), even though they are all in the same zone, us-central1-c.

Exit and disable all the resources that you wont need to avoid charges.