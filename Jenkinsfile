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
    }
}
