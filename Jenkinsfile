pipeline {
    agent any
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
                sh 'pwd'
                sh 'ls -l'
//                sh 'docker build us-central1-docker.pkg.dev/indigo-syntax-375116/acg/train-schedule'
                sh 'docker build -t us-central1-docker.pkg.dev/indigo-syntax-375116/acg/train-schedule .'
            }
        }
        stage('Push Docker Image') {
            when {
                branch 'master'
            }
            steps {
                sh 'docker push us-central1-docker.pkg.dev/indigo-syntax-375116/acg/train-schedule'
            }
        }
        stage('DeployToProduction') {
            when {
                branch 'master'
            }
            steps {
                input 'Deploy to Production?'
                milestone(1)
                withCredentials([usernamePassword(credentialsId: 'webserver_login', usernameVariable: 'USERNAME', passwordVariable: 'USERPASS')]) {
                    script {
                        sh "sshpass -p '$USERPASS' -v ssh -o StrictHostKeyChecking=no $USERNAME@$prod_ip \"docker pull us-central1-docker.pkg.dev/indigo-syntax-375116/acg/train-schedule\""
                        try {
                            sh "sshpass -p '$USERPASS' -v ssh -o StrictHostKeyChecking=no $USERNAME@$prod_ip \"docker stop train-schedule\""
                            sh "sshpass -p '$USERPASS' -v ssh -o StrictHostKeyChecking=no $USERNAME@$prod_ip \"docker rm train-schedule\""
                        } catch (err) {
                            echo: 'caught error: $err'
                        }
                        sh "sshpass -p '$USERPASS' -v ssh -o StrictHostKeyChecking=no $USERNAME@$prod_ip \"docker run --restart always --name train-schedule -p 8080:8080 -d us-central1-docker.pkg.dev/indigo-syntax-375116/acg/train-schedule\""
                    }
                }
            }
        }        
    }   
}
