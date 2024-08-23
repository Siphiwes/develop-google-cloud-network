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

gcloud compute networks create griffin-dev-vpc --subnet-mode=custom

gcloud compute networks subnets create griffin-dev-wp \
    --network=griffin-dev-vpc \
    --range=192.168.16.0/20 \
    --region=$REGION

gcloud compute networks subnets create griffin-dev-mgmt \
    --network=griffin-dev-vpc \
    --range=192.168.32.0/20 \
    --region=$REGION




