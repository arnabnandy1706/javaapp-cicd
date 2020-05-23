pipeline {
    agent none
    stages {
        stage('maven-test') {
            agent {
                docker {
                    image 'arnabdnandy1706/jdk-mvn-jenkins-agent:0.2'
                    args '-i --entrypoint='
                }
            }
            steps {
                sh '''
                    cd application
                    mvn test
                '''
            }
        }
        stage('maven-install') {
            agent {
                docker {
                    image 'arnabdnandy1706/jdk-mvn-jenkins-agent:0.2'
                    args '-i --entrypoint='
                }
            }
            steps {
                sh '''
                    cd application
                    mvn clean install
                '''
            }
        }
        stage('upload-artifact') {
            agent {
                docker {
                    image 'arnabdnandy1706/jdk-mvn-jenkins-agent:0.2'
                    args '-i --entrypoint='
                }
            }
            steps {
                echo "Uploading the Artifact to Minio Server"
                sh '''
                    cd application/target
                    minio-upload articats knote-java-1.0.0.jar 52.66.26.10:9001
                '''
            }
        }
        stage('docker-build-push') {
            agent { label 'AWS' }
            steps {
                echo "Downloading the Artifacts and building the Docker image and pushing it to the Container Registry"
                sh '''
                    cd application
                    minio-download artifacts knote-java-1.0.0.jar 52.66.26.10:9001
                    docker build -t knote-app:latest .
                    docker tag knote-app:latest arnabdnandy1706/knote-app:latest
                    docker push arnabdnandy1706/knote-app:latest
                '''
            }
        }
        stage('deploy-kubernetes') {
            agent { label 'AWS' }
            steps {
                echo "Deploying the Application on Kubernetes"
                sh '''
                    cd application/kube
                    kubectl apply -f mongo.yaml
                    kubectl apply -f knote.yaml 
                '''
            }
        }
    }
}