pipeline {
    agent any
    environment {
        //be sure to replace "willbla" with your own Docker Hub username
        DOCKER_IMAGE_NAME = "stallonejoel/train-schedule"
    }
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation'
                sh './gradlew build --no-daemon'
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
        }
        stage('Build Docker Image') {
            when {
                branch 'master'
            }
            steps {
                script {
                    app = docker.build(DOCKER_IMAGE_NAME)
                    app.inside {
                        sh 'echo Hello, World!'
                    }
                }
            }
        }
        
      stage ('DeployToProduction') {
    when {
        branch 'master'
    }
    steps {
        input 'Deploy to Production'
        milestone(1)
        withCredentials ([usernamePassword(credentialsId: 'kubemaster', usernameVariable: 'USERNAME', passwordVariable: 'USERPASS')]) {
            script {
                sh "sshpass -p '$USERPASS' scp -r -o StrictHostKeyChecking=no train-schedule-kube.yml $USERNAME@${env.kubemasterip}:/home/cloud_user"
                try {
			sh "sshpass -p '$USERPASS' -v ssh -o StrictHostKeyChecking=no $USERNAME@${env.kubemasterip} kubectl apply -f /home/cloud_user/train-schedule-kube.yml"
                } catch (err) {
                    echo: 'caught error: $err'
                }
            }
        }
    }
  }
 }
}
