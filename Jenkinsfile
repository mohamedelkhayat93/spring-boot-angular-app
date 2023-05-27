pipeline {
    agent any

    stages {
        stage('Authenticate with AWS EKS') {
            steps {
                withAWS(region: '<define the aws region>', roleAccount: '<IAM that jenkins use to be able to communicate with the cluster>') {
                    sh 'aws eks update-kubeconfig --name <your-cluster-name>'
                }
            }
        }

        stage('Deploy postgres using helm ') {
            steps {
                sh 'helm upgrade --install pstsql-test bitnami/postgresql --set postgresqlUsername=postgres,postgresqlPassword=root --namespace <namespace_name>'
            }
        }

        stage('build the backend service ') {
            steps {
                echo "building jar file ..."
                sh """
                    cd ./backend
                    mvn package

                """
                echo "building Docker image ..."
                dockerImage = docker.build("<docker_image_name>:latest", \
                  "--force-rm=true -f ${WORKSPACE}/Dockerfile .")
            }
        }
        stage('Deploy the backend service then delete the local docker image') {
            steps {
                sh """
                    cd ./
                    helm upgrade --install backend backend-chart/ --namespace <namespace_name>
                    docker rmi <docker_image_name>:latest
                """
            }
        }
        stage('build the frontend service ') {
            steps {
                echo "building frontend files ..."
                sh """
                    cd ./frontend
                    npm install
                    ng build --prod

                """
                echo "building Docker image ..."
                dockerImage = docker.build("<docker_image_name>:latest", \
                  "--force-rm=true -f ${WORKSPACE}/Dockerfile .")
            }
        }
        stage('Deploy the frontend service then delete the local docker image') {
            steps {
                sh """
                    cd ./
                    helm upgrade --install frontend frontend-chart/ --namespace <namespace_name>
                    docker rmi <docker_image_name>:latest
                """
            }
        }
    }
    post {
    always {
        echo 'cleaning up workspace ...'
        cleanWs()
    }
    success {
        echo 'all OK !'
    }
    unstable {
        echo 'build pipeline is unstable ...'
    }
    failure {
        echo 'build pipeline has failed ...'
    }
    }
}