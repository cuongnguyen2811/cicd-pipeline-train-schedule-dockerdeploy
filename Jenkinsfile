pipeline {
    agent any
    tools {
        gradle 'Gradle_5.0'
    }
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation'
                sh 'gradle --version'
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
                    sh 'groupadd docker'
                    sh 'usermod -aG docker root'
                    sh 'chmod 666 /var/run/docker.sock'
                    app = docker.build("cuong2811/train-schedule")
                    app.inside {
                        sh 'echo $(curl localhost:8000)'
                    }
                }
            }
        }
        stage('Push Docker Image') {
            when {
                branch 'master'
            }
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'docker_hub_login') {
                        app.push("${env.BUILD_NUMBER}")
                        app.push("latest")
                    }
                }
            }
        }
    }
}
