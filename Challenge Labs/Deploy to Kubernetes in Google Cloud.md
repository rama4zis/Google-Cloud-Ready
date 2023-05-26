# Task 1. Create a Docker image and store the Dockerfile

```
source <(gsutil cat gs://cloud-training/gsp318/marking/setup_marking_v2.sh)

gcloud source repos clone valkyrie-app

cd ~/marking/valkyrie-app

cat > Dockerfile <<EOF
FROM golang:1.10
WORKDIR /go/src/app
COPY source .
RUN go install -v
ENTRYPOINT ["app","-single=true","-port=8080"]
EOF

docker build -t <DOCKER IMAGE>:<TAG NAME> .

bash ~/marking/step1_v2.sh
```

# Task 2. Test the created Docker image

```
docker run -p 8080:8080 <DOCKER IMAGE>:<TAG NAME>
bash ~/marking/step2_v2.sh
```

# Task 3. Push the Docker image to the Artifact Registry

```
gcloud artifacts repositories create <REPOSITORY> \
    --repository-format=docker \
    --location=us-central1 \
    --description="subcribe to quciklab" \
    --async 

gcloud auth configure-docker us-central1-docker.pkg.dev

docker images
```
*Copy the IMAGE ID of the image you want to push to Artifact Registry*

```
docker tag <IMIAGE ID> us-central1-docker.pkg.dev/<Google Cloud Project ID>/<REPOSITORY>/<DOCKER IMAGE>:<TAG NAME>

docker push us-central1-docker.pkg.dev/<Google Cloud Project ID>/<REPOSITORY>/<DOCKER IMAGE>:<TAG NAME>

```

# Task 4. Create and expose a deployment in Kubernetes

```
sed -i s#IMAGE_HERE#us-central1-docker.pkg.dev/<Google Cloud Project ID>/<REPOSITORY>/<DOCKER IMAGE>:<TAG NAME>#g k8s/deployment.yaml

gcloud container clusters get-credentials valkyrie-dev --zone us-east1-d
kubectl create -f k8s/deployment.yaml
kubectl create -f k8s/service.yaml
```