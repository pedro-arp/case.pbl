pipeline {
    agent any
    options {
        skipStagesAfterUnstable()
    }
    parameters {
        booleanParam(name: 'SKIP_TESTS', defaultValue: true, description: 'Skip tests during build')
    }
    stages {
        stage('Build') {
            steps {
                echo 'building the application...'
                sh "mvn -B -DskipTests=${params.SKIP_TESTS} clean package"
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
