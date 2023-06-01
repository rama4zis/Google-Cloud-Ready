# 1. Create a custom security role

Follow the steps in the lab to create a custom security role.

```
nano custom-role.yaml
```

```
title: "orca_storage_update"
description: "Permissions"
stage: "ALPHA"
includedPermissions:
- storage.buckets.get
- storage.objects.get
- storage.objects.list
- storage.objects.update
- storage.objects.create
```

```
gcloud iam roles create <YOUR ORCA STORAGE CONTROLLER> --project $DEVSHELL_PROJECT_ID \
--file role-definition.yaml
```

# 2. Create a service account. & 3. Bind a custom security role to a service account.

```
gcloud iam service-accounts create <YOUR ORCA PRIVATE> --display-name "my service account"

gcloud alpha projects add-iam-policy-binding $DEVSHELL_PROJECT_ID --member serviceAccount:<YOUR ORCA PRIVATE>@$DEVSHELL_PROJECT_ID.iam.gserviceaccount.com --role "projects/<YOUR PROJECT ID>/roles/orca_storage_update"

gcloud projects add-iam-policy-binding $DEVSHELL_PROJECT_ID --member serviceAccount:<YOUR ORCA PRIVATE>@$DEVSHELL_PROJECT_ID.iam.gserviceaccount.com --role "roles/monitoring.viewer"

gcloud projects add-iam-policy-binding $DEVSHELL_PROJECT_ID --member serviceAccount:<YOUR ORCA PRIVATE>@$DEVSHELL_PROJECT_ID.iam.gserviceaccount.com --role "roles/logging.logWriter"

gcloud projects add-iam-policy-binding $DEVSHELL_PROJECT_ID --member serviceAccount:<YOUR ORCA PRIVATE>@$DEVSHELL_PROJECT_ID.iam.gserviceaccount.com --role "roles/monitoring.metricWriter"
```

# 4. Create and configure a new Kubernetes Engine private cluster

```
gcloud container clusters create <YOUR ORCA CLUSTER> --num-nodes 1 --master-ipv4-cidr=172.16.0.64/28 --network orca-build-vpc --subnetwork orca-build-subnet --enable-master-authorized-networks  --master-authorized-networks 192.168.10.2/32 --enable-ip-alias --enable-private-nodes --enable-private-endpoint --service-account orca-private-cluster-sa@<YOUR PROJECT ID>.iam.gserviceaccount.com --zone us-east1-b
```

# 5. Deploy an application to a private Kubernetes Engine cluster.

1.  Navigate to VM instances in the console.
2.  Click SSH *orca-jumphost*
3.  We need to install gke-gcloud-auth-plugin before running any kubectl commands.
```
sudo apt-get install google-cloud-sdk-gke-gcloud-auth-plugin
echo "export USE_GKE_GCLOUD_AUTH_PLUGIN=True" >> ~/.bashrc
source ~/.bashrc

gcloud container clusters get-credentials <YOUR ORCA CLUSTER> --internal-ip --zone us-east1-b --project <YOUR PROJECT ID>

kubectl create deployment hello-server --image=gcr.io/google-samples/hello-app:1.0

kubectl expose deployment hello-server --type=LoadBalancer --port 8080
```