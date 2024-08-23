**Task1: Create development VPC manually**

Create a VPC called griffin-dev-vpc with the following subnets only:
_riffin-dev-wp_

IP address block: 192.168.16.0/20
_griffin-dev-mgmt_
IP address block: 192.168.32.0/20

Exporing variables:

export REGION=us-east1

export ZONE=us-east1-b

export ADDITIONAL_ENGINEER_EMAIL=engineer@griffin.com

USING CLOUD SHELL:

gcloud compute networks create griffin-dev-vpc --subnet-mode=custom

gcloud compute networks subnets create griffin-dev-wp \
    --network=griffin-dev-vpc \
    --range=192.168.16.0/20 \
    --region=$REGION

gcloud compute networks subnets create griffin-dev-mgmt \
    --network=griffin-dev-vpc \
    --range=192.168.32.0/20 \
    --region=$REGION

USING CLOUD CONSOLE:

Go to **network** > **Create VPC network**
Name it griffin-dev-vpc.
Add subnets **griffin-dev-wp (192.168.16.0/20)** and **griffin-dev-mgmt (192.168.32.0/20)**.
Click **Create.**

# Task 2: Create production VPC manually

Create a VPC called griffin-prod-vpc with the following subnets only:

griffin-prod-wp --IP address block: 192.168.48.0/20

griffin-prod-mgmt --IP address block: 192.168.64.0/20

USING CLOUD SHELL:

gcloud compute networks create griffin-prod-vpc --subnet-mode=custom

gcloud compute networks subnets create griffin-prod-wp \
    --network=griffin-prod-vpc \
    --range=192.168.48.0/20 \
    --region=$REGION

gcloud compute networks subnets create griffin-prod-mgmt \
    --network=griffin-prod-vpc \
    --range=192.168.64.0/20 \
    --region=$REGION

USING CLOUD CONSOLE:

Go to **VPC network** >**Create VPC network**

Name it **griffin-prod-vpc**

Add subnets **griffin-prod-wp (192.168.48.0/20)** and **griffin-prod-mgmt (192.168.64.0/20)**
Click **Create**

# Task 3. Create bastion host

Create a bastion host with two network interfaces, one connected to griffin-dev-mgmt and the other connected to griffin-prod-mgmt. Make sure you can SSH to the host.

USING CLOUD SHELL:

gcloud compute instances create griffin-bastion \
    --machine-type=e2-medium \
    --zone=$ZONE \
    --tags=bastion \
    --network-interface=subnet=griffin-dev-mgmt \
    --network-interface=subnet=griffin-prod-mgmt \
    --metadata=startup-script='#! /bin/bash
        sudo apt-get update
        sudo apt-get install -yq git htop
    ' \
    --scopes=cloud-platform \
    --image-family=debian-10 \
    --image-project=debian-cloud

USING CLOUD CONSOLE:

Go to **VM instances** > **Create instance** > Name it **griffin-bastion** > Select e2-medium as the machine type.

Under **Networking**, add two network interfaces:

1. The first interface connected to griffin-dev-mgmt
2. The second interface connected to griffin-prod-mgmt
   
Allow SSH connections.

Lastly **Create**

Once done, create firewall rules allowing TCP traffic on port 22:

VIA CLOUD SHELL:

gcloud compute firewall-rules create griffin-dev-allow-ssh \
    --network=griffin-dev-vpc \
    --allow=tcp:22 \
    --source-ranges=0.0.0.0/0 \
    --target-tags=bastion \
    --description="Allow SSH access to bastion host"

gcloud compute firewall-rules create griffin-prod-allow-ssh \
    --network=griffin-prod-vpc \
    --allow=tcp:22 \
    --source-ranges=0.0.0.0/0 \
    --target-tags=bastion \
    --description="Allow SSH access to bastion host in production"

VIA CLOUD CONSOLE:

Go to **Firewall** > **Create a Firewall rule** > Name it griffin-dev-allow-ssh, set the network to griffin-dev-vpc, and allow tcp:22 from 0.0.0.0/0.

Repeat same step for griffin-prod-allow-ssh in griffin-prod-vpc.


# Task 4. Create and configure Cloud SQL Instance

VIA CLOUD SHELL:

gcloud sql instances create griffin-dev-db \
    --database-version=MYSQL_5_7 \
    --tier=db-n1-standard-1 \
    --region=$REGION

gcloud sql databases create wordpress --instance=griffin-dev-db

gcloud sql users create wp_user --host=% --instance=griffin-dev-db --password=password123

gcloud sql connect griffin-dev-db --user=root << EOF
CREATE DATABASE wordpress;
CREATE USER 'wp_user'@'%' IDENTIFIED BY 'stormwind_rules';
GRANT ALL PRIVILEGES ON wordpress.* TO 'wp_user'@'%';
FLUSH PRIVILEGES;
EOF

VIA CLOUD CONSOLE:

Go to SQL >> Create instance >> MySQL and configure the instance with the name griffin-dev-db.

Choose the appropriate region and settings.

Create the wordpress database and wp_user user with necessary privileges using the SQL command interface.

# Task 5. Create Kubernetes cluster

VIA CLOUD SHELL:

gcloud container clusters create griffin-dev \
    --zone=$ZONE \
    --num-nodes=2 \
    --machine-type=e2-standard-4 \
    --network=griffin-dev-vpc \
    --subnetwork=griffin-dev-wp

VIA CLOUD CONSOLE:

Go to Kubernetes Engine >> Click Create Cluster >> Select Standard Cluster.

Name it griffin-dev.

Set the Node Pools:

Machine type: e2-standard-4

Number of nodes: 2

Set the Network to griffin-dev-vpc and Subnetwork to griffin-dev-wp.

**Create**

# Task 6. Prepare the Kubernetes cluster

From Cloud Shell copy all files using gsutil cp -r gs://cloud-training/gsp321/wp-k8s.

**Change to the directory and list the files:**

cd wp-k8s
ls



