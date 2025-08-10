# Learner Report Card - PRD
It is Mern application to track and show the progress of learnings for each registered lerners.
It has backend that uses mongo db is storage solution.Frontend talks to backend.
Backend Port: 3001
Frontend Port: 3000

**🛠️ Tech Stack**

nodejs – App codinging language

Docker – Building Docker Image

K8s/Minikube – For Deployment


****Set Up****

Two Different Git Repos were merged into single Repo.

Created .env and Docker files for each backend and front end services.

docker build -t amolkhetan/frontend-lrccapstone .
docker build -t amolkhetan/backendend-lrccapstone .

docker push  amolkhetan/frontend-lrccapstone
docker push  amolkhetan/backend-lrccapstone

docker run -p 3001:3001 amolkhetan/backend-lrccapstone
docker run -p 3000:3000 amolkhetan/frontend-lrccapstone

****Local testing****

<img width="1913" height="148" alt="image" src="https://github.com/user-attachments/assets/c93f1e42-39b4-4a50-8f3a-bfe29d584255" />

<img width="1908" height="109" alt="image" src="https://github.com/user-attachments/assets/fdacda34-8ea4-4ed1-81a1-ce63ae56e6c7" />

<img width="1919" height="972" alt="image" src="https://github.com/user-attachments/assets/d0db8530-8950-4133-84ba-e8e593bf22a1" />

****Helm-Chart****
Now, Create Helm-chart using below command and create/update deployment,service files for k8s deployment.

**#Create Chart#**

helm create learner-reportcs-charts

**#Package it#**

helm package learner-reportcs-charts

**#Install helm package#**
helm install learner-report ./learner-reportcs-charts-0.1.0.tgz

****Jenkins File****
Created Jenkins script and attached in Repo itself.
Deployment can be done either in EKS or Docker Desktop.
Jenkins files are available for both.

Jenkins logs are also attached in repo

<img width="1879" height="943" alt="image" src="https://github.com/user-attachments/assets/6d1891b5-0dc7-4dd8-906c-f7c475b888b8" />

****Deployment****
EKS Cluster
<img width="1900" height="495" alt="image" src="https://github.com/user-attachments/assets/ab6fadab-a8ca-4a8e-a790-6b83d1151bbe" />

Deployment Status
<img width="1905" height="259" alt="image" src="https://github.com/user-attachments/assets/b564be25-38a3-43c0-aca3-f36392bf66c7" />

**Testing**
FrontEnd was validated using LB endpoint and port 3000

<img width="1910" height="959" alt="image" src="https://github.com/user-attachments/assets/3a3feff8-b0d8-466f-9931-3ed36dfa2a4b" />

**Note**: I am trying to do changes in jenkins file to check if cluster is present or not, if not then create before deploy. Also, wanted to use mongodb is on cloud  but wanted to use it from cluster itself. But the jenkins is getting stuck due to space issue and hence submitting it without testing the final changes but will do it as and when jenkins issue is resolved.

<img width="1918" height="846" alt="image" src="https://github.com/user-attachments/assets/01a06bac-b79c-464b-b7e7-a4f79726a964" />






