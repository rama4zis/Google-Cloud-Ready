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
  - Name: $InstanceName
  - Region: $DefaultRegion
  - Zone: $DefaultZone
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

Create a zonal cluster using default zone
```bash
gcloud container clusters create nucleus-webserver1 \
    --zone $DefaultZone

gcloud container clusters get-credentials nucleus-webserver1

kubectl create deployment hello-app \
    --image=gcr.io/google-samples/hello-app:2.0

kubectl expose deployment hello-app \
    --type=LoadBalancer \
    --port $AppPort

kubectl get service
```

