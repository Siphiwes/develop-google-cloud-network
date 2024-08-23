# develop-google-cloud-network
# As a cloud engineer at Jooli Inc. and recently trained with Google Cloud and Kubernetes, you have been asked to help a new team (Griffin) set up their environment. The team has asked for your help and has done some work, but needs you to complete the work.

You are expected to have the skills and knowledge for these tasks.

# What you need to do:
Create a development VPC with three subnets manually
Create a production VPC with three subnets manually
Create a bastion that is connected to both VPCs
Create a development Cloud SQL Instance and connect and prepare the WordPress environment
Create a Kubernetes cluster in the development VPC for WordPress
Prepare the Kubernetes cluster for the WordPress environment
Create a WordPress deployment using the supplied configuration
Enable monitoring of the cluster
Provide access for an additional engineer

Some Jooli Inc. standards you should follow:
Create all resources in the REGION region and ZONE zone, unless otherwise directed.
Use the project VPCs.
Naming is normally team-resource, e.g. an instance could be named kraken-webserver1.
Allocate cost effective resource sizes. Projects are monitored and excessive resource use will result in the containing project's termination (and possibly yours), so beware. This is the guidance the monitoring team is willing to share: unless directed, use e2-medium.
