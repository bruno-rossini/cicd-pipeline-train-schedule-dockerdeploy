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
    }   
}
