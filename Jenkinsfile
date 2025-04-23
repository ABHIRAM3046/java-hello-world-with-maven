pipeline {
    agent any

    tools {
        maven 'Maven'
    }
    stages {
        stage('Checkout Code') {
            steps {
                git 'https://github.com/ABHIRAM3046/java-hello-world-with-maven.git'
            }
        }
        stage('Build with Maven') {
            steps {
                sh 'mvn clean package'
            }
        }
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE .'
            }
        }
        stage('Push to Docker Hub') {
            steps {
                withDockerRegistry(credentialsId: 'Docker-hub', url: '') {
                    sh '''
                        docker push abhiram3046/java-app-sample:$Build_Number
                        docker tag abhiram3046/java-app-sample:$Build_Number abhiram3046/java-app-sample:latest
                        docker push abhiram3046/java-app-sample:latest
                    '''
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                sshagent(['kube-deploy']) {
                    sh """
                        ssh -o StrictHostKeyChecking=no ubuntu@34.230.176.42 << 'ENDSSH'
                            rm -rf Kubernetes-Mainfests-java
                            git clone https://github.com/ABHIRAM3046/Kubernetes-Mainfests-java.git
                            cd Kubernetes-Mainfests-java/Manifests
                            kubectl apply -f deployment.yaml
                            kubectl apply -f service.yaml
                    """
                }
            }
        }
    }
}
