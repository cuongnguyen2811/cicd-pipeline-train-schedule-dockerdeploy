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
        stage('DeployToProduction') {
            when {
                branch 'master'
            }
            steps {
                input 'Deploy to Production?'
                milestone(1)
                sshagent(['ssh-remote']) {
                    script {
                        sh """ssh -l root @$prod_ip -o StrictHostKeyChecking=no  docker pull cuong2811/train-schedule:${env.BUILD_NUMBER}"""
                        try {
                            sh 'ssh -l root @$prod_ip -o StrictHostKeyChecking=no docker stop train-schedule'
                            sh 'ssh -l root @$prod_ip -o StrictHostKeyChecking=no docker rm train-schedule'
                        } catch (err) {
                            echo: 'caught error: $err'
                        }
                        sh """ssh -l root @$prod_ip -o StrictHostKeyChecking=no docker run --restart always --name train-schedule -p 8000:8000 -d cuong2811/train-schedule:${env.BUILD_NUMBER}"""
                    }
                }
            }
        }
    }
}
