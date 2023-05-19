Shell

```
gcloud config set project $(gcloud projects list --format='value(PROJECT_ID)' --filter='qwiklabs-gcp')

git clone https://github.com/rosera/pet-theory.git
```

# Task 1. Create a Firestore database

*Navigation Menu > Firestore*
1.  Native mode
2.  Choose a location (See the task requirements for the location)
3.  Create database

# Task 2. Populate the Database

Shell

```
cd ~/pet-theory/lab06/firebase-import-csv/solution
npm install
node index.js netflix_titles_original.csv
```

# Task 3. Create a REST API

Shell

```
cd ~/pet-theory/lab06/firebase-rest-api/solution-01
npm install
gcloud builds submit --tag gcr.io/$GOOGLE_CLOUD_PROJECT/rest-api:0.1
gcloud beta run deploy netflix-dataset-service-625 --image gcr.io/$GOOGLE_CLOUD_PROJECT/rest-api:0.1 --allow-unauthenticated
```

**Choose enable for the API**
**Choose the region us-central1**
**Remember, your Cloud Run Service name maybe different**

# Task 4. Firestore API access

Shell
    
```
cd ~/pet-theory/lab06/firebase-rest-api/solution-02
npm install
gcloud builds submit --tag gcr.io/$GOOGLE_CLOUD_PROJECT/rest-api:0.2
gcloud beta run deploy netflix-dataset-service-625 --image gcr.io/$GOOGLE_CLOUD_PROJECT/rest-api:0.2 --allow-unauthenticated
```
**Copy the URL from the output**
```
SERVICE_URL=<Your URL>
curl -X GET $SERVICE_URL/2019
```

# Task 5. Deploy the Staging Frontend

Shell

```
cd ~/pet-theory/lab06/firebase-frontend/public
nano app.js # Comment line 4 and uncomment liine 5 then Change the SERVICE_URL to your URL
npm install
cd ~/pet-theory/lab06/firebase-frontend
gcloud builds submit --tag gcr.io/$GOOGLE_CLOUD_PROJECT/frontend-staging:0.1
gcloud beta run deploy frontend-staging-service-153 --image gcr.io/$GOOGLE_CLOUD_PROJECT/frontend-staging:0.1
```

**Maybe your Cloud Run Service name is different**
**Allow unauthenticated invocations to the service**

# Task 6. Deploy the Production Frontend

Shell

```
gcloud builds submit --tag gcr.io/$GOOGLE_CLOUD_PROJECT/frontend-production:0.1
gcloud beta run deploy frontend-production-service-416 --image gcr.io/$GOOGLE_CLOUD_PROJECT/frontend-production:0.1
```

**Maybe your Cloud Run Service name is different**
**Allow unauthenticated invocations to the service**
