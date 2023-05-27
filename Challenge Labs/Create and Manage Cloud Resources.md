Initialize the environment

```bash
export DefaultRegion=<Default Region>
export DefaultZone=<Default Zone>
export InstanceName=<Instance name>
export AppPort=<App Port Number>
export FirewallRuleName=<Firewall Rule Name>
```

# 1. Create a project jumphost instance

- Go to Compute Engine > VM Instances
- Create a new instance with the following settings:
  - Name: <InstanceName>
  - Region: <DefaultRegion>
  - Zone: <DefaultZone>
  - Series: N1
  - Machine type: f1-micro
  - Boot disk: Make it default (Debian)
- Click Create

# 2. Create a Kubernetes service cluster

Set default region and zone

```bash
gcloud config set compute/region $DefaultRegion
gcloud config set compute/zone $DefaultZone
```

In this step, you have to create a Kubernetes Service Cluster

*Create the cluster in the us-central1 region. *Using the Docker container hello-app (gcr.io/google-samples/ hello-app:2.0) as a place holder. *Open the app on port 8080 *Activate Cloud Shell and write the following commands

```bash
gcloud config set compute/zone $DefaultZone
gcloud container clusters create nucleus-jumphost-webserver1
gcloud container clusters get-credentials nucleus-jumphost-webserver1
kubectl create deployment hello-app --image=gcr.io/google-samples/hello-app:2.0
kubectl expose deployment hello-app --type=LoadBalancer --port $AppPort
kubectl get service
```

# 3. Setup an HTTP load balancer

You will serve the site via nginx web servers, but you want to ensure that the environment is fault-tolerant. Create an HTTP load balancer with a managed instance group of 2 nginx web servers. Use the following code to configure the web servers; the team will replace this with their own configuration later.

```bash
cat << EOF > startup.sh
#! /bin/bash
apt-get update
apt-get install -y nginx
service nginx start
sed -i -- 's/nginx/Google Cloud Platform - '"\$HOSTNAME"'/' /var/www/html/index.nginx-debian.html
EOF
```

Next follow the steps :
1.  Create an instance template.
```bash
gcloud compute instance-templates create nginx-template \
    --metadata-from-file startup-script=startup.sh
```

2.  Create a target pool.
```bash
gcloud compute target-pools create nginx-pool
```

3.  Creating a managed instance group.
```bash
gcloud compute instance-groups managed create nginx-group \
    --base-instance-name nginx \
    --size 2 \
    --template nginx-template \
    --target-pool nginx-pool 

gcloud compute instances list
```

4.  Create a firewall rule to allow traffic (80/tcp).
```bash
gcloud compute firewall-rules create $FirewallRuleName --allow tcp:80

gcloud compute forwarding-rules create nginx-lb \
    --region $DefaultRegion \
    --ports=80 \
    --target-pool nginx-pool

gcloud compute forwarding-rules list
```

5.  Create a health check.
```bash
gcloud compute http-health-checks create http-basic-checkgcloud compute instance-groups managed \
    set-named-ports nginx-group \
    --named-ports http:80
```

6.  Creating a backend service and attach the managed instance group
```bash
gcloud compute backend-services create nginx-backend \
    --protocol HTTP --http-health-checks http-basic-check --global

gcloud compute backend-services add-backend nginx-backend \
    --instance-group nginx-group \
    --instance-group-zone $DefaultZone \
    --global
```

7.  Create a URL map and target HTTP proxy to route requests to your URL map.
```bash
gcloud compute url-maps create web-map \
    --default-service nginx-backend 

gcloud compute target-http-proxies create http-lb-proxy \
    --url-map web-map
```

8.  Create a forwarding rule.
```bash
gcloud compute forwarding-rules create http-content-rule\
    --global \
    --target-http-proxy http-lb-proxy \
    --ports 80 

gcloud compute forwarding-rules list
```