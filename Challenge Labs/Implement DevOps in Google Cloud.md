# 1. Create the lab resources

Make initial

```bash
export PROJECT_ID=
export CLUSTER_NAME=
export ZONE=
export REGION=
export REPO=
```

1.  Run the following command to enable the APIs for GKE, Cloud Build, and Cloud Source Repositories:
    
    ```bash
    gcloud services enable container.googleapis.com \
    cloudbuild.googleapis.com \
    sourcerepo.googleapis.com
    ```

2.  Add the Kubernetes Developer role for the Cloud Build service account:

    ```bash
    export PROJECT_ID=$(gcloud config get-value project)
    gcloud projects add-iam-policy-binding $PROJECT_ID \
    --member=serviceAccount:$(gcloud projects describe $PROJECT_ID \
    --format="value(projectNumber)")@cloudbuild.gserviceaccount.com --role="roles/container.developer"
    ```

3.  Run the following to configure Git in Cloud Shell, replacing <email> with your generated lab email address and <name> with your name.

    ```bash
    git config --global user.email "<email>"
    git config --global user.name "<name>"
    ```

4.  Create an Artifact Registry Docker repository named my-repository in the us-central1 region to store your container images.

    ```bash
    gcloud artifacts repositories create my-repository \
    --repository-format=docker \
    --location=$REGION
    ```

5.  Create a GKE cluster named hello-cluster with the following configuration:

    ```bash
    gcloud beta container --project "$PROJECT_ID" clusters create "$CLUSTER_NAME" --zone "$ZONE" --no-enable-basic-auth --cluster-version latest --release-channel "regular" --machine-type "e2-medium" --image-type "COS_CONTAINERD" --disk-type "pd-balanced" --disk-size "100" --metadata disable-legacy-endpoints=true  --logging=SYSTEM,WORKLOAD --monitoring=SYSTEM --enable-ip-alias --network "projects/$PROJECT_ID/global/networks/default" --subnetwork "projects/$PROJECT_ID/regions/$REGION/subnetworks/default" --no-enable-intra-node-visibility --default-max-pods-per-node "110" --enable-autoscaling --min-nodes "2" --max-nodes "6" --location-policy "BALANCED" --no-enable-master-authorized-networks --addons HorizontalPodAutoscaling,HttpLoadBalancing,GcePersistentDiskCsiDriver --enable-autoupgrade --enable-autorepair --max-surge-upgrade 1 --max-unavailable-upgrade 0 --enable-shielded-nodes --node-locations "$ZONE"
    ```

6.  Create the prod and dev namespaces on your cluster.

    ```bash
    kubectl create namespace prod
    kubectl create namespace dev
    ```

# 2. Create a repository in Cloud Source Repositories

# 3. Create the Cloud Build Triggers

Navigate to Cloud Build > Triggers and click Create Trigger.

```
Trigger name: sample-app-prod-deploy
Repository: Select your repository
Branch: ^master$
```
Click Create Trigger.


Make another trigger for the dev branch.
```
Trigger name: sample-app-dev-deploy
Repository: Select your repository
Branch: ^dev$

```
Click Create Trigger.


# 4. Deploy the first versions of the application