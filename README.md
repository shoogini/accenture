EXP 1:
git init 
git add .
git commit -m "initial commit"
git remote add origin https://github.com/manojmr23/ABC.git
git push -u origin main. If this error occurs, use this command ( git branch -m main) and redo this push  command 

git checkout -b feature-branch
echo "Feature branch update" >> demo.txt
git add .
git commit -m "Added demo.txt for pull request test"
git push origin feature-branch


EXP 2:
Exp 2 :
mkdir docker-demo
cd docker-demo

create index.html file 

create docker file 

FROM nginx 
COPY index.html /usr/share/nginx/html/index.html

docker build -t myweb:v1 .

docker run -d -p 9090:80 myweb:v1



EXP 3:
flask-docker-app/
├── app.py
├── requirements.txt
└── Dockerfile

app.py 

from flask import Flask, render_template_string

app = Flask(_name_)
@app.route('/')
def home():
    return render_template_string("<h1>Welcome to the Flask Docker App!</h1>")
if _name_ == '_main_':
    app.run(host='0.0.0.0', port=5000)

requirements.txt

Flask==2.2.5


Dockerfile

FROM python:3.9-slim
WORKDIR /app
COPY requirements.txt requirements.txt
RUN pip install -r requirements.txt
COPY . .
EXPOSE 5000
CMD ["python", "app.py"]


docker build -t flask-docker-app .

docker run -p 5000:5000 flask-docker-app



EXP 4:
mkdir jenkins-static-site   
cd jenkins-static-site 

create docker-compose.yml

version: '3.8'

services:
  jenkins:
    image: jenkins/jenkins:lts
    container_name: jenkins
    restart: unless-stopped
    ports:
      - "8080:8080"
      - "50000:50000"
    volumes:
      - jenkins_home:/var/jenkins_home
      - ./jenkins-config:/usr/share/jenkins/ref
      - /var/run/docker.sock:/var/run/docker.sock
volumes:
  jenkins_home:
    driver: local


docker compose up -d


docker exec jenkins cat /var/jenkins_home/secrets/initialAdminPassword


if any error came u can do this 
docker compose down
>> docker compose up -d





EXP 5:

mkdir python-ci-docker-lab
cd python-ci-docker-lab

python-ci-docker-lab/
│
├── _init_.py   ✅ (empty file)
├── app.py
├── requirements.txt
├── Dockerfile
├── .gitignore
│
├── tests/
│   └── test_app.py
│
└── .github/
    └── workflows/
        └── ci-dockerhub.yml

STEP 2 — Create Python Files
app.py
def add(a, b):
    return a + b

if _name_ == "_main_":
    print("Hello from Python CI Lab!")
    print("2 + 3 =", add(2, 3))

tests/test_app.py
import sys
import os
# ✅ Add the project root to Python's module path
sys.path.append(os.path.abspath(os.path.join(os.path.dirname(_file_), '..')))
from app import add
def test_add():
    assert add(2, 3) == 5
    assert add(-1, 1) == 0

requirements.txt
pytest==8.3.2

Dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
CMD ["python", "app.py"]

gitignore
_pycache_/
.venv/
.pytest_cache/
.DS_Store
*.pyc

ci-dockerhub.yml

name: ci-dockerhub
on:
  push:
    branches: [ "main" ]
    tags: [ "*" ]
  pull_request:
    branches: [ "main" ]
jobs:
  build-test-push:
    runs-on: ubuntu-latest
    steps:
    # Step 1: Checkout code
    - name: Checkout
      uses: actions/checkout@v4
    # Step 2: Set up Python 3.11
    - name: Set up Python
      uses: actions/setup-python@v5
      with:
        python-version: '3.11'
    # Step 3: Install dependencies
    - name: Install deps
      run: |
        python -m pip install --upgrade pip
        pip install -r requirements.txt
    # Step 4: Run tests
    - name: Run tests
      run: pytest -q
    # Step 5: Set Docker image metadata
    - name: Docker meta
      id: meta
      uses: docker/metadata-action@v5
      with:
        # ✅ Hardcode your Docker Hub username to prevent invalid tags
        images: manojmr232005/python-ci-lab
        tags: |
          type=raw,value=latest,enable={{is_default_branch}}
          type=sha,prefix=sha-,format=short
          type=ref,event=tag
    # Step 6: Set up QEMU (for multi-arch builds)
    - name: Set up QEMU
      uses: docker/setup-qemu-action@v3
    # Step 7: Set up Docker Buildx
    - name: Set up Docker Buildx
      uses: docker/setup-buildx-action@v3
    # Step 8: Login to Docker Hub
    - name: Login to Docker Hub
      uses: docker/login-action@v3
      with:
        username: ${{ secrets.DOCKERHUB_USERNAME }}
        password: ${{ secrets.DOCKERHUB_TOKEN }}
    # Step 9: Build and push the Docker image
    - name: Build and push
      uses: docker/build-push-action@v6
      with:
        context: .
        push: true
        tags: ${{ steps.meta.outputs.tags }}
        labels: ${{ steps.meta.outputs.labels }}
        platforms: linux/amd64

This file tells GitHub Actions:
Test Python code
Build Docker image
Push it to Docker Hub automatically
______________
🧪 STEP 4 — Test Locally (Optional)
Run manually to confirm before CI:
python app.py
python -m pytest -q
docker build -t yourname/python-ci-lab:local .
docker run --rm yourname/python-ci-lab:local

STEP 5 — Initialize Git & Push
git init
git add .
git commit -m "init lab"
git branch -M main
git remote add origin https://github.com/<your-username>/python-ci-docker-lab.git
git push -u origin main


git commit --allow-empty -m "retrigger: fix Docker Hub login"
>> git push



Manage Kubernetes Resources Using CLI

A. Install Docker
Windows:
1. Download and install Docker Desktop from Docker's website.
2. Enable WSL2 backend during installation.
3. Start Docker Desktop and ensure it is running.

Verify Docker installation:
docker --version

B. Install kubectl (Kubernetes CLI)
Windows (using Chocolatey):

choco install kubernetes-cli

Verify installation:
kubectl version --client

C. Install and Start Minikube
Windows (using Chocolatey):

choco install minikube
minikube start --driver=docker

Verify the cluster is running:

minikube status
kubectl get nodes

If kubectl get nodes returns a node in Ready state — you are ready to go!

🧩 Step 1: Create a Simple Pod

Create a file: nginx-pod.yaml

apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  labels:
    app: nginx
spec:
  containers:
    - name: nginx
      image: nginx:latest
      ports:
        - containerPort: 80


Apply the manifest:

kubectl apply -f nginx-pod.yaml

Check pod status:

kubectl get pods
# expected: nginx-pod 1/1 Running

Describe the pod (to see detailed info):

kubectl describe pod nginx-pod

Forward the port locally to access it in the browser:

kubectl port-forward pod/nginx-pod 8080:80

Then open http://localhost:8080 in your browser.
When done, delete the pod:

kubectl delete pod nginx-pod

🧩 Step 2: Deployment and Scaling
Create a deployment:

kubectl create deployment my-nginx --image=nginx

Check deployments and pods:

kubectl get deployments

kubectl get pods -l app=my-nginx

Scale to 3 replicas:

kubectl scale deployment my-nginx --replicas=3
kubectl get pods

Update the image (rolling update):

kubectl set image deployment/my-nginx nginx=nginx:1.25
kubectl rollout status deployment/my-nginx

If something goes wrong, rollback:
kubectl rollout undo deployment/my-nginx

🧩 Step 3: Expose Deployment as a Service
Expose the deployment using a NodePort service:

kubectl expose deployment my-nginx --type=NodePort --port=80
kubectl get svc

Get the URL of your app using Minikube:

minikube service my-nginx --url

Open the displayed URL in your browser.

To remove the service:
kubectl delete svc my-nginx

🧹 Step 4: Cleanup
After the lab, clean up all created resources:
kubectl delete deployment my-nginx
kubectl delete svc my-nginx
kubectl delete all --all -n default

If you want to stop or delete the cluster:
minikube stop
minikube delete

🧾 Summary
In this lab, you learned how to:
1. Install Docker, kubectl, and Minikube.
2. Create and manage Pods.
3. Deploy and scale applications.
4. Expose Deployments as services.
5. Clean up resources safely.
