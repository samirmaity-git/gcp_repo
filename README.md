# gcp_repo

Create a Kubernetes service cluster

student_00_4fde6f91e024@cloudshell:~ (qwiklabs-gcp-01-434b7d21f29f)$ gcloud config set compute/region us-east1
Updated property [compute/region].
student_00_4fde6f91e024@cloudshell:~ (qwiklabs-gcp-01-434b7d21f29f)$ gcloud config set compute/zone us-east1-b
Updated property [compute/zone].

create a gke cluster. this will take sometime.

gcloud container clusters create --machine-type=e2-medium lab-cluster 

authenticate with cluster

gcloud container clusters get-credentials lab-cluster 

create deployment from gcr image

kubectl create deployment hello-server --image=gcr.io/google-samples/hello-app:2.0

expose application to external traffic
kubectl expose deployment hello-server --type=LoadBalancer --port 8080

inspect service

kubectl get service

check your application.
http://[EXTERNAL-IP]:8080





create the load balancer template:

gcloud compute instance-templates create lb-backend-template1 \
   --region=us-east1 \
   --network=default \
   --subnet=default \
   --tags=allow-health-check \
   --machine-type=e2-medium \
   --image-family=debian-11 \
   --image-project=debian-cloud \
   --metadata=startup-script="#! /bin/bash
apt-get update
apt-get install -y nginx
service nginx start
sed -i -- 's/nginx/Google Cloud Platform - '"\$HOSTNAME"'/' /var/www/html/index.nginx-debian.html"


Create a managed instance group.

cloud compute instance-groups managed create lb-backend-group \
   --template=lb-backend-template1 --size=2 --region=us-east1
   
Create a firewall rule named as permit-tcp-rule-582 to allow traffic (80/tcp).

gcloud compute firewall-rules create permit-tcp-rule-582 \
  --network=default \
  --action=allow \
  --direction=ingress \
  --target-tags=allow-health-check \
  --rules=tcp:80


Create a health cehck for load balancer

gcloud compute health-checks create http http-basic-check \
  --port 80
  
  
  create a backend service
  
  gcloud compute backend-services create web-backend-service \
  --protocol=HTTP \
  --port-name=http \
  --health-checks=http-basic-check \
  --global
  
  
  attach the managed instance group with named port (http:80).
  
  gcloud compute backend-services add-backend web-backend-service   --instance-group=lb-backend-group   --instance-group-zone=asia-southeast1-b   --global
  
  
  
