# Task 1. Download the monolith code and build your container

Clone repo and running setup script
```
git clone https://github.com/googlecodelabs/monolith-to-microservices
cd monolith-to-microservices
./setup.sh
```

To run the monolith project use the following commands from the top level directory
```
cd monolith
npm start
```

if the monolith is running correctly, you can close the application with `Ctrl+C`

Now enable the cloud build api, then make Name your artifact as follows
```
gcloud services enable cloudbuild.googleapis.com
gcloud builds submit --tag gcr.io/${GOOGLE_CLOUD_PROJECT}/<Monolith Identifier>:1.0.0 .
```

# Task 2. Create a kubernetes cluster and deploy the application

Create your cluster as follows
```
gcloud config set compute/zone us-central1-a
gcloud services enable container.googleapis.com
gcloud container clusters create <CLUSTER NAME> --num-nodes 3
```

Create and expose your deployment
```
kubectl create deployment <Monolith Identifier> --image=gcr.io/${GOOGLE_CLOUD_PROJECT}/<Monolith Identifier>:1.0.0
kubectl expose deployment <Monolith Identifier> --type=LoadBalancer --port 80 --target-port 8080
```

# Task 3. Create new microservices

Create a containerized version of your microservices
```
cd ~/monolith-to-microservices/microservices/src/orders
gcloud builds submit --tag gcr.io/${GOOGLE_CLOUD_PROJECT}/<Orders Identifier>:1.0.0 .

cd ~/monolith-to-microservices/microservices/src/products
gcloud builds submit --tag gcr.io/${GOOGLE_CLOUD_PROJECT}/<Products Identifier>:1.0.0 .
```

# Task 4. Deploy the new microservices

Create and expose your deployments
```
kubectl create deployment fancy-orders-277 --image=gcr.io/${GOOGLE_CLOUD_PROJECT}/fancy-orders-277:1.0.0
kubectl expose deployment fancy-orders-277 --type=LoadBalancer --port 80 --target-port 8081

kubectl create deployment fancy-products-261 --image=gcr.io/${GOOGLE_CLOUD_PROJECT}/fancy-products-261:1.0.0
kubectl expose deployment fancy-products-261 --type=LoadBalancer --port 80 --target-port 8082
```

You can verify that the services are running using your external IP addresses

To check the external IP addresses, use the following command
```
kubectl get svc -w
```
http://ORDERS_EXTERNAL_IP/api/orders
http://PRODUCTS_EXTERNAL_IP/api/products

# Task 5. Configure and deploy the Frontend microservice

Creat variables for the external IP addresses
```
export ORDERS_SERVICE_IP=$(kubectl get services -o jsonpath="{.items[1].status.loadBalancer.ingress[0].ip}")
export PRODUCTS_SERVICE_IP=$(kubectl get services -o jsonpath="{.items[2].status.loadBalancer.ingress[0].ip}")
```

Now rebuild the frontend app
```
cd ~/monolith-to-microservices/react-app
sed -i "s/localhost:8081/$ORDERS_SERVICE_IP/g" .env
sed -i "s/localhost:8082/$PRODUCTS_SERVICE_IP/g" .env
npm run build
```

# Task 6. Create a containerized version of the Frontend microservice

Use Cloud Build to package up the contents of the Frontend service and push it up to the Google Container Registry
```
cd ~/monolith-to-microservices/microservices/src/frontend
gcloud builds submit --tag gcr.io/${GOOGLE_CLOUD_PROJECT}/<Frontend Identifier>:1.0.0 .
```

# Task 7. Deploy the Frontend microservice

```
kubectl create deployment <Frontend Identifier> --image=gcr.io/${GOOGLE_CLOUD_PROJECT}/<Frontend Identifier>:1.0.0
kubectl expose deployment <Frontend Identifier> --type=LoadBalancer --port 80 --target-port 8080

kubectl get svc -w
```