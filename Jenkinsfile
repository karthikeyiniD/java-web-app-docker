pipeline {
    agent any

    stages {
        stage('Checkout from SCM') {
            steps {
               git url: 'https://github.com/karthikeyiniD/java-web-app-docker.git',branch: 'main'
            }
        }   
        stage("Maven Clean Package"){
            steps {
                script {
                    def mavenHome = tool name: "Maven", type: "maven"
                    def mavenCMD = "${mavenHome}/bin/mvn"
                    sh "${mavenCMD} clean package "
                }
            }
        }
         stage('Build Docker Image') {
            steps {
                script {
                    // Build Docker image
                    sh "docker build -t karthikeyinid/java-web-app-docker:Java-Project-${BUILD_NUMBER} ."
                }
            }
        }

        stage('Docker Login and Push Image to Docker Hub') {
            steps {
                script {
                     withCredentials([string(credentialsId: 'Docker_Hub_Pwd', variable: 'Docker_Hub_Pwd')]) {
          sh "docker login -u karthikeyinid -p ${Docker_Hub_Pwd}"
        }
                    // push Docker image
                    sh "docker push  karthikeyinid/java-web-app-docker:Java-Project-${BUILD_NUMBER} "
                }
            }
        }
        stage('EKS Deploy') {
            steps {
                sh '''
                    aws eks update-kubeconfig --name demo --region us-east-1
                    sed "s/buildNumber/${BUILD_NUMBER}/g" deploy.yml > deploy-new.yml
                    kubectl apply -f deploy-new.yml
                    kubectl apply -f svc.yml
                '''
            }
        }
    }
}
