pipeline {
    agent any

    tools {
        maven 'M2_HOME'
    }

    environment {
        IMAGE_NAME = "azizbenammar11/student-management"
        TAG = "1.0.0"
        NAMESPACE = "devops"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/azizbenammar7/BenAmmar_Aziz_4SIM1.git'
            }
        }

        stage('Build with Maven') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:$TAG .'
            }
        }

        stage('Docker Login & Push') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-credentials',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                      echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                      docker push $IMAGE_NAME:$TAG
                    '''
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                  kubectl apply -f k8s-manifests/ -n devops
                  kubectl rollout status deployment/student-app -n devops
                '''
            }
        }

        stage('Test Application') {
            steps {
                sh '''
                  curl http://192.168.49.2:30089/student/Depatment/getAllDepartment
                '''
            }
        }
    }

    post {
        success {
            echo "🎉 CI/CD terminé avec succès"
        }
        failure {
            echo "❌ Échec du pipeline"
        }
    }
}
