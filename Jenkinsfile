pipeline {
  agent any

  environment {
    IMAGE = "your-dockerhub-username/my-flask-app"
    EC2_HOST = "ubuntu@your-ec2-ip"
    PEM_FILE = "your-key.pem"
  }

  stages {
    stage('Clone Repo') {
      steps {
        git 'https://github.com/your-username/my-devops-project.git'
      }
    }

    stage('Build Docker Image') {
      steps {
        sh 'docker build -t $IMAGE .'
      }
    }

    stage('Push Docker Image') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
          sh '''
            echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
            docker push $IMAGE
          '''
        }
      }
    }

    stage('Deploy to EC2') {
      steps {
        sshagent(['ec2-ssh-key']) {
          sh 'scp -o StrictHostKeyChecking=no deploy.sh $EC2_HOST:~'
          sh 'ssh -o StrictHostKeyChecking=no $EC2_HOST "chmod +x deploy.sh && ./deploy.sh"'
        }
      }
    }
  }
}
