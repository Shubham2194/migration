<img width="783" alt="image" src="https://github.com/user-attachments/assets/93cd139f-7bd8-4902-857a-20af2318c4e9" />

Here we are going to create a docker image which supports
multiple platforms i.e linux/amd64 and linux/arm64.

According to Docker official Documents Docker supports building container images targeting multiple platforms,
Docker images are Compatible with multiple CPU architectures.


Let's START >>> 🚀🚀🚀🚀🚀🚀🚀

STEP 1: Make sure you are using Docker >= 20.10, fire below command to check version.
```
docker version
```


<img width="656" alt="image" src="https://github.com/user-attachments/assets/1d520b01-d64d-4906-bf7a-193adfd41e10" />



(i have Docker version 20.10.5+dfsg1, build 55c4c88, The +dfsg1 indicates Debian-distributed version of Docker, which is known to disable some features, including CLI plugins like buildx)

STEP 2: Uninstall docker and reinstall arm64 comapatible docker version

```
dpkg -l | grep -i docker

for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do sudo apt-get remove $pkg; done
sudo apt-get purge -y docker-engine docker docker.io docker-ce docker-ce-cli docker-compose-plugin
sudo apt-get autoremove -y --purge docker-engine docker docker.io docker-ce docker-compose-plugin

sudo rm -rf /var/lib/docker /etc/docker
sudo rm /etc/apparmor.d/docker
sudo groupdel docker
sudo rm -rf /var/run/docker.sock
sudo rm -rf /var/lib/containerd
sudo rm -r ~/.docker
```

STEP 3: Setup Docker's apt repository 

For Ubuntu :

```
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```

For Debian:

```
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/debian/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/debian \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```


STEP 4: Install docker 

```
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

<img width="1556" alt="image" src="https://github.com/user-attachments/assets/a5d27f0b-f8bc-49b9-9482-230af26bdbf4" />

STEP 5: docker version 

```
docker version
```

<img width="1674" alt="image" src="https://github.com/user-attachments/assets/0895ca0e-8163-4a07-a18a-3c889964bd2e" />


STEP 6: Fix permission denied issue

```
# Add current user to docker group
newgrp docker
sudo usermod -aG docker $USER
sudo usermod -aG docker jenkins
sudo systemctl restart docker
sudo systemctl restart jenkins

```

STEP 7: These should now work without sudo 

```
docker ps
docker buildx version
```
<img width="1048" alt="image" src="https://github.com/user-attachments/assets/fc2ee811-c07e-4450-a9e8-c4c157d3f4d2" />


STEP 8: Try running buildx command

```
docker buildx ls
```

as we can see platforms linux/amd64 (+4) and linux/386
<img width="974" alt="image" src="https://github.com/user-attachments/assets/1c3bd201-2582-4a60-86d0-54d26be51cfc" />

STEP 9 : Deploy ARM64 compatible docker container as pod on EKS 

NOTE: Make sure you have graviton nodegroup added to your cluster and add Label to the node as name = "graviton"

Dockerfile:
(i am deploying python fast API)

```
FROM python:3.11-slim

# Install system dependencies
RUN apt-get update && apt-get install -y \
    build-essential \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

WORKDIR /code

COPY source_requirements.txt /code/
RUN pip3 install --no-cache-dir -r source_requirements.txt

COPY . /code

CMD ["python", "main.py"]
```

STEP 10: Let's build Dockerfile from Jenkins, made small change in build stage of Jenkins:

(if you are using local , just run below command to build and tag it )
```
docker buildx build --platform linux/arm64 -f Dockerfile_graviton . --load
```

--load flag will :

🔹 Builds the image

🔹 Tags it

✅ Loads it into the local Docker engine so you can use docker tag and docker push manually

for jenkins add below in your build stage: 

```
docker buildx create --use || true
docker buildx build \
  --platform linux/arm64 \
  --file Dockerfile_source \
  --tag ${REPOSITORY_URI}:${IMAGE_TAG} \
  --cache-from=type=registry,ref=${REPOSITORY_URI}:buildcache \
  --cache-to=type=registry,ref=${REPOSITORY_URI}:buildcache,mode=max \
  --push .
```

i have below ennvironment variables in jenkinsfile:

```
IMAGE_REPO_NAME="<your ecr registry name>"
AWS_DEFAULT_REGION="<your aws region>"
REPOSITORY_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}"
IMAGE_TAG = "1.${env.BUILD_NUMBER}-${PIPELINE_NAME}"
```

STEP 11: Once you are able to create build lets use the same Image and add Affinity to schedule pod to graviton node.
(remove vault annotation if you are using kubernetes secrets and change containerPort accordingly)

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: graviton
spec:
  replicas: 1
  selector:
    matchLabels:
      app: graviton
  template:
    metadata:
      annotations:
        vault.hashicorp.com/agent-inject: "true"
        vault.hashicorp.com/role: "dev"
        vault.hashicorp.com/agent-inject-secret-secrets.env: "dev/data/secret"
        vault.hashicorp.com/agent-inject-template-secrets.env: |
          {{- with secret "dev/data/secret" -}}
          {{- range $k, $v := .Data.data }}
          export {{ $k }}="{{ $v }}"
          {{- end }}
          {{- end }}      
      labels:
        app: graviton
    spec:
      serviceAccountName: dev
      affinity:
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
              - matchExpressions:
                  - key: name
                    operator: In
                    values:
                      - graviton
      containers:
        - name: graviton
          image: <Graviton docker Image>
          resource-gravitons: 
            requests:
              memory: 1Gi
              cpu: "0.1"
            limits:
              memory: 2Gi
              cpu: "0.5"
          imagePullPolicy: IfNotPresent
          ports:
            - containerPort: 5000
      imagePullSecrets:
      - name: ecr-private-image-secret
```

STEP 12: Deploy in EKS
(If you are using ArgoCD within Cluster to deploy your pod like me use below , either just run : kubectl apply -f deployment.yaml)

(i have artifacts Github where i have graviton directory in which i have my manifest files.)
```
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: graviton
  namespace: argocd
spec:
  destination:
    name: in-cluster
    namespace: backend  
    server: 'https://kubernetes.default.svc'
  project: default
  source:
    repoURL: 'https://github.com/tlabsai/artifacts.git'
    path: graviton
    targetRevision: HEAD
  destination:
    server: 'https://kubernetes.default.svc'
    namespace: backend
  syncPolicy:
    automated:
      prune: true
      selfHeal: false
---
apiVersion: v1
kind: Service
metadata:
  creationTimestamp: null
  labels:
    app:  graviton
  name: graviton
spec:
  ports:
  - port: 80
    protocol: TCP
    targetPort: 5000
  selector:
    app: graviton
  type: ClusterIP
```

```
kubectl apply -f argocd.yaml
```

STEP 13: Now let's check logs and see if pod scheduled to Graviton node

(graviton node)

<img width="772" alt="image" src="https://github.com/user-attachments/assets/c90716f7-cade-453f-8d71-ed0fce457f23" />

<img width="789" alt="image" src="https://github.com/user-attachments/assets/8f057c1f-2f89-43e8-93c4-ea11e85723fa" />


🚀✨ Finally! We are able to successfully migrate and deploy our container to Graviton nodes 💪🎉

⚡️ These ARM-based instances are cost-efficient 💸 and deliver better performance 🔥 compared to traditional x86-based nodes.

🌱 A solid step toward optimized, scalable, and sustainable infrastructure! 🛠️🌍
