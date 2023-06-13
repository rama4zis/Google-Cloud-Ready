# Task 1. Create a Pub/Sub topic

```
export BUCKET_NAME=<YOUR_BUCKET_NAME>
export TOPIC_ID=<YOUR_TOPIC_NAME>
export PROJECT_ID=<YOUR_PROJECT_ID>

gcloud config set compute/region "REGION"

gcloud services disable dataflow.googleapis.com

gcloud services enable dataflow.googleapis.com

PROJECT_ID=$(gcloud config get-value project)
REGION=us-central1
AE_REGION=us-central


gsutil mb gs://$BUCKET_NAME

gcloud pubsub topics create $TOPIC_ID

gcloud app create --region=$AE_REGION
```

# Task 2. Create a Cloud Scheduler job

```
gcloud scheduler jobs create pubsub publisher-job --schedule="* * * * *" \
    --topic=$TOPIC_ID --message-body="<YOUR CHALLENGE LAB MESSAGE HERE>"
```

# Task 3. Create a Cloud Storage bucket

```
gcloud scheduler jobs run publisher-job

git clone https://github.com/GoogleCloudPlatform/java-docs-samples.git
cd java-docs-samples/pubsub/streaming-analytics


mvn compile exec:java \
-Dexec.mainClass=com.examples.pubsub.streaming.PubSubToGcs \
-Dexec.cleanupDaemonThreads=false \
-Dexec.args=" \
    --project=$PROJECT_ID \
    --region=$REGION \
    --inputTopic=projects/$PROJECT_ID/topics/$TOPIC_ID \
    --output=gs://$BUCKET_NAME/samples/output \
    --runner=DataflowRunner \
    --windowSize=2"
```

# Task 4. Run a Dataflow pipeline to stream data from a Pub/Sub topic to Cloud Storage

Wait for "All workers have finished the startup process and began to receive work request."

Navigate to Dataflow UI > Job History

Make sure the job status is Running