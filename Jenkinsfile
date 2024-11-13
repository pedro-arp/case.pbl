pipeline {
    agent any
    tools {
        maven 'M2_HOME'
    }
    options {
        skipStagesAfterUnstable()
    }
    stages {
        stage('Build') {
            steps {
                echo 'building the application...'
                sh 'mvn -B -DskipTests clean package'
            }
        }
        stage('Test') {
            steps {
                echo 'testing the application...'
                sh 'mvn test'
            }
            post {
                always {
                    echo 'collecting test results...'
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }
        stage('Deploy') {
            steps {
                echo 'deploying the application...'
                sh './jenkins/scripts/deliver.sh'
            }
        }
    }
}
