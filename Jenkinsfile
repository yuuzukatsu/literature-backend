def branch = "production"
def remoteurl = "https://github.com/yuuzukatsu/literature-backend.git"
def remotename = "jenkins"
def workdir = "~/literature-backend/"
def ip = "10.71.15.183"
def username = "dimasf"
def imagename = "yuuzukatsu/literature-backend"
def sshkeyid = "app-key"
def composefile = "backend-compose.yml"

pipeline {
    agent any

    stages {
        stage('Pull From Backend Repo') {
            steps {
                sshagent(credentials: ["${sshkeyid}"]) {
                    sh """
                        ssh -l ${username} ${ip} <<pwd
                        cd ${workdir}
                        git remote add ${remotename} ${remoteurl} || git remote set-url ${remotename} ${remoteurl}
                        git pull ${remotename} ${branch}
                        pwd
                    """
                }
            }
        }
            
        stage('Build Docker Image') {
            steps {
                sshagent(credentials: ["${sshkeyid}"]) {
                    sh """
                        ssh -l ${username} ${ip} <<pwd
                        cd ${workdir}
                        docker build -t ${imagename}:latest .
                        pwd
                    """
                }
            }
        }
            
        stage('Deploy Image') {
            steps {
                sshagent(credentials: ["${sshkeyid}"]) {
                    sh """
                        ssh -l ${username} ${ip} <<pwd
                        cd ${workdir}
                        docker compose up -f ${composefile} -d
                        pwd
                    """
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                sshagent(credentials: ["${sshkeyid}"]) {
			sh """
				ssh -l ${username} ${ip} <<pwd
				docker image push ${imagename}:latest
				docker image prune
				pwd
			"""
		}
            }
        }


        // stage('Send Success Notification') {
        //     steps {
        //         sh """
        //             curl -X POST 'https://api.telegram.org/bot${env.telegramapi}/sendMessage' -d \
		//     'chat_id=${env.telegramid}&text=Build ID #${env.BUILD_ID} Backend Pipeline Successful!'
        //         """
        //     }
        // }

    }
}
