# Online Shop Application

#### A full-stack Online Shop web application using Spring Boot 2 and Angular 7 with postgres database 
I've clone it from the internet 
## Technology Stacks
**Backend**
  - Java 11
  - Spring Boot 2.2
  - Spring Security
  - JWT Authentication
  - Spring Data JPA
  - Hibernate
  - PostgreSQL
  - Maven

**Frontend**
  - Angular 7
  - Angular CLI
  - Bootstrap

## How to  Run

Start the backend server before the frontend client.  

  
Note: The backend API url is configured in `src/environments/environment.ts` of the frontend project. It is `localhost:8080/api` by default.
  
#### Run in Docker
You can build the image and run the container with Docker. 
1. Build backend project
```bash
cd backend
mvn package
```
2. Build fontend project
```bash
cd frontend
npm install
ng build --prod
```
3. Build images and run containers
```bash
docker-compose up --build
```
### helm charts 
1. to deploy the postgres database on kubernets cluster we use the helm chart available on the internet https://charts.bitnami.com/bitnami
```
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update 
helm upgrade --install pstsql-test bitnami/postgresql --set postgresqlUsername=postgres,postgresqlPassword=root --namespace <namespace_name>
```

2. to deploy the backend we have a local helm chart called backend-chart that can be used to deploy the backend microservice 
```
helm upgrade --install backend backend-chart/ --namespace <namespace_name>
```
3. to deploy the frontend we have a local helm chart called frontend-chart that can be used to deploy the frontend microservice 
```
helm upgrade --install frontend frontend-chart/ --namespace <namespace_name>
```

### CI/CD pipeline
In order to build and deploy this application on aws EKS cluster we have a jenkins pipeline written by a Jenkinsfile that do the following:

1. authenticate with EKS cluster 
2. Deploy the postgres DB using helm from bitnami repo 
3. Build the backend service
4. Deploy the backend service
5. Build the frontend service 
6. Deploy the frontend service 

### In case the data retrieval is slow?

here I will provide some reasons from my experience and solutions to fix them :
1. Network latency: If the client is far away from the aws region where the application is hosted network latency can cause slow data retrieval.

CloudFront is a good solution for reducing network latency,it reduces website or application delivery time, including can be used for dynamic, static, streaming content.. So it let the client receive its data before entering the vpc of our resources 

2. Database performance: If the database is not optimized for performance, data retrieval can be slow.

We can use RDS managed sql aws service.

Read replicas for RDS can improve performance.The load generated with the queries related to reading the database are handled by these replicas and this eventually enhances the overall performance of the RDS.

Redis can enhance the performance of AWS RDS. It will be used as a cache for frequently accessed data to reduce the load on the RDS server.

3. Overloaded backend or frontend resources 

We will use horizontal pod autoscaler (HPA) object to avoid slow data retrieval for application. This feature automatically scales up or down the number of pods based on specified metric (CPU,memory,etc ….). By using HPA  you can ensure that your application has the right amount of resources to handle the load.

