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

Add a legacy HTTP health check resource:

gcloud compute http-health-checks create basic-check

Add a target pool in the same region as your instances. Run the following to create the target pool and use the health check, which is required for the service to function:

  gcloud compute target-pools create www-pool \
    --region  --http-health-check basic-check

Add the instances to the pool:

gcloud compute target-pools add-instances www-pool \
    --instances www1,www2,www3
    
    
Create a managed instance group.

gcloud compute instance-groups managed create lb-backend-group --template=lb-backend-template1 --size=2 --region=us-east1
   
  
   
   
Create a firewall rule named as permit-tcp-rule-582 to allow traffic (80/tcp).

gcloud compute firewall-rules create permit-tcp-rule-790 \
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
  
  Add your instance group as the backend to the backend service:

gcloud compute backend-services add-backend web-backend-service   --instance-group=lb-backend-group   --instance-group-region=us-east1   --global
  
  
Create a URL map to route the incoming requests to the default backend service:

gcloud compute url-maps create web-map-http \
    --default-service web-backend-service
    
    Create a target HTTP proxy to route requests to your URL map:

gcloud compute target-http-proxies create http-lb-proxy \
    --url-map web-map-http
    
    Create a global forwarding rule to route incoming requests to the proxy:

gcloud compute forwarding-rules create http-content-rule \
    --address=lb-ipv4-1\
    --global \
    --target-http-proxy=http-lb-proxy \
    --ports=80
    
    
  
  
