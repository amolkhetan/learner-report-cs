# Learner Report Card - PRD
It is Mern application to track and show the progress of learnings for each registered lerners.
It has backend that uses mongo db is storage solution.Frontend talks to backend.
Backend Port: 3001
Frontend Port: 3000

****Set Up****

Two Different Git Repos were merged into single Repo using below set of commands:


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

#Create Chart
helm create learner-reportcs-charts

#Package it
helm package learner-reportcs-charts

#Install helm package
helm install learner-report ./learner-reportcs-charts-0.1.0.tgz

****Jenkins File****
Created Jenkins script and attached in Repo itself.
Deployment can be done either in EKS or Docker Desktop.
Jenkins files are available for both.
