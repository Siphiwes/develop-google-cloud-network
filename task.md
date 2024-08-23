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




