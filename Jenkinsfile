pipeline {
    agent any
    environment {
        SERVICE = 'auth'
        NAME = "laupontiroli/${env.SERVICE}"
    }
    stages {
        stage('Dependecies') {
            steps {
                build job: 'auth', wait: true
            }
        }
        stage('Build') { 
            steps {
                sh 'mvn -B -DskipTests clean package'
            }
        }      
        stage('Build & Push Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credential', usernameVariable: 'USERNAME', passwordVariable: 'TOKEN')]) {
                    sh "docker login -u $USERNAME -p $TOKEN"
                    sh "docker buildx create --use --platform=linux/arm64,linux/amd64 --node multi-platform-builder-${env.SERVICE} --name multi-platform-builder-${env.SERVICE}"
                    sh "docker buildx build --platform=linux/arm64,linux/amd64 --push --tag ${env.NAME}:latest --tag ${env.NAME}:${env.BUILD_ID} -f Dockerfile ."
                    sh "docker buildx rm --force multi-platform-builder-${env.SERVICE}"
                }
            }
        }
        // stage('Deploy') { 
        //     steps {
        //         sh 'kubectl apply -f k8s/secret.yaml'
        //         sh 'kubectl apply -f k8s/service.yaml'
        //         sh 'kubectl apply -f k8s/deployment.yaml'
        //     }
        // }  
    }
}
