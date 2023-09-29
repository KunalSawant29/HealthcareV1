pipeline {
  agent any

  tools {
    maven 'M2_HOME'
    
    }
  environment {
        AWS_ACCESS_KEY_ID     = credentials('AWS_ACCESS_KEY_ID')
        AWS_SECRET_ACCESS_KEY = credentials('AWS_SECRET_ACCESS_KEY') 
  } 
  
  stages {
   stage('Git CheckOut') {
      steps {
        echo 'Checkout the source code from GitHub'
        git 'https://github.com/KunalSawant29/HealthcareV1.git'
            }
    }
    
    stage('Package the Application') {
      steps {
        echo " Packaging the application"
        sh 'mvn clean package'
            }
    }
    
    stage('Publish Reports using HTML') {
      steps {
      publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: '/var/lib/jenkins/workspace/Healthcare/target/surefire-reports', reportFiles: 'index.html', reportName: 'HTML Report', reportTitles: '', useWrapperFileDirectly: true])
            }
    }
    
    stage('Docker Image Creation') {
      steps {
        sh 'docker build -t kunalsawant29/healthcare:1.0 .'
            }
    }
    stage('DockerLogin') {
      steps {
        withCredentials([string(credentialsId: 'dockerhub-pwd', variable: 'dockerhubpwd')]) {
        sh 'docker login -u kunalsawant29 -p ${dockerhubpwd}'
            }
        }
    } 
  
    stage('Push Image to DockerHub') {
      steps {
        sh 'docker push kunalsawant29/healthcare:1.0'
            }
    } 
        stage ('Configure Test-server with Terraform, Ansible and Deploy'){
            steps {
                dir('my-serverfiles'){
                sh 'sudo chmod 600 myownkey.pem'
                sh 'terraform init'
                sh 'terraform validate'
                sh 'terraform apply --auto-approve'
                }
            }
        }
     }
}
