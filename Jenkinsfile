pipeline{
	agent any
	
	triggers {
		githubPush()
	}
	
	environment {
		DOCKERHUB_CREDENTIALS = credentials('dockerhub')
		DROPLET_IP = credentials('droplet_1_ip')
		REMOTE_SERVER_DOMAIN = 'jenkins-slave-1.silk-cat.software'
	}

	stages {
		stage('gitclone') {

			steps {
				git branch: 'main', url: 'https://github.com/hovanvydut/nodeapp_test.git'
			}
		}

		stage('Build') {

			steps {
				sh 'docker build -t hovanvydut/test-jenkins:amd64 .'
			}
		}

		stage('Login') {

			steps {
				sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
			}
		}

		stage('Push') {

			steps {
				sh 'docker push hovanvydut/test-jenkins:amd64'
			}
		}

		stage('Pull and run container') {
			steps {
				script {
					sshagent(credentials: ['droplet_1_private_key']) {
						sh '''
							ssh -o StrictHostKeyChecking=no -l root $REMOTE_SERVER_DOMAIN uname -a &&
							docker system prune --force --all --volumes &&
							docker rm -f test &&
							docker run -d --name=test -p 3000:3000 hovanvydut/test-jenkins:amd64
						'''
					}
				}
			}
		}
	}

	post {
		always {
            echo 'Always message'
		}

		success {
            echo 'Success message'
        }

		failure {
            echo 'Failed :( message'
        }

        changed {
            echo 'Things were different before...'
        }

		aborted  {
			echo "Aborted message"
		}
	}
}