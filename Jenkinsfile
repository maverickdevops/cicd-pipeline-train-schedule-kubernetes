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
        
        stage('DeployToProduction') {
            when {
                branch 'master'
            }
		steps{
			input 'Deploy to Production?'
			milestone(1)
			sshagent([‘kubeconfig’]){
				sh ‘scp -r -o StrictHostKeyChecking=no train-schedule-kube.yml cloud_user@13.54.189.89’
			}
					script{
						try{
							sh ‘ssh cloud_user@13.54.189.89 kubectl apply -f train-schedule-kube.yml’
						}catch(error){
						}
					}
				}
			}

            }
        }
    }
}
