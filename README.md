# Create-and-Manage-Cloud-Resources-Challenge-lab-2############################ Getting-Started-Create-and-Manage-Cloud-Resources-Challenge-Lab ########################


Task1

gcloud compute instances create [Input_Given_Instance_name] \
          --network nucleus-vpc \
          --zone us-east1-b  \
          --machine-type f1-micro  \
          --image-family debian-9  \
          --image-project debian-cloud
          
 --------------------------------------------------------
 
 Task2
 
 Part1


gcloud config set compute/zone us-east1-b


gcloud container clusters create nucleus-cluster
            
--------------------------------------------------------------
Part2



kubectl create deployment hello-server \
          --image=gcr.io/google-samples/hello-app:2.0
          
---------------------------------------------------------------          
Part3


kubectl expose deployment hello-server \
          --type=LoadBalancer \
          --port [Input_Given_Port_Number]

--------------------------------------------------------------

Task3

Part1


cat << EOF > startup.sh
#! /bin/bash
apt-get update
apt-get install -y nginx
service nginx start
sed -i -- 's/nginx/Google Cloud Platform - '"\$HOSTNAME"'/' /var/www/html/index.nginx-debian.html
EOF

----------------------------------------------------------------
Part2

gcloud compute instance-templates create web-server-template \
          --metadata-from-file startup-script=startup.sh \
          --network nucleus-vpc \
          --machine-type f1-micro \
          --region us-east1

-----------------------------------------------------------------
Part 3

gcloud compute instance-groups managed create web-server-group \
          --base-instance-name web-server \
          --size 2 \
          --template web-server-template \
          --region us-east1

------------------------------------------------------------------
Part4

gcloud compute firewall-rules create [Input_Given_Firewall_Name] \
          --allow tcp:80 \
          --network nucleus-vpc \
          --rules=tcp:80

-------------------------------------------------------------------
Part5

          
          
gcloud compute http-health-checks create http-basic-check
gcloud compute instance-groups managed \
          set-named-ports web-server-group \
          --named-ports http:80 \
          --region us-east1

------------------------------------------------------------------
Part6 


gcloud compute backend-services create web-server-backend \
          --protocol HTTP \
          --http-health-checks http-basic-check \
          --global
          
--------------------------------------------------------------------
Part7
          
gcloud compute backend-services add-backend web-server-backend \
          --instance-group web-server-group \
          --instance-group-region us-east1 \
          --global

-------------------------------------------------------------------
Part8

gcloud compute url-maps create web-server-map \
          --default-service web-server-backend
          
          
-------------------------------------------------------------------
Part9
          
          
gcloud compute target-http-proxies create http-lb-proxy \
          --url-map web-server-map

-------------------------------------------------------------------
Part 10


gcloud compute forwarding-rules create http-content-rule \
        --global \
        --target-http-proxy http-lb-proxy \
        --ports 80
gcloud compute forwarding-rules list
