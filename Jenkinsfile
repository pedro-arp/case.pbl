pipeline {
    agent any

    tools {
        maven 'M2_HOME'  // Substitua pelo nome exato da instalação do Maven configurado no Jenkins
        jdk 'JAVA_HOME'         // Substitua pelo nome exato da instalação do JDK configurado no Jenkins
    }

  environment {
        GITHUB_CREDENTIALS = credentials('demo-app-git-credentials')  // Certifique-se que o ID está correto
    }

    stages {
        stage('Checkout') {
            steps {
                // Realiza o checkout do código usando as credenciais configuradas para o GitHub
                checkout([$class: 'GitSCM', branches: [[name: '*/master']],
                          userRemoteConfigs: [[url: 'https://github.com/pedro-arp/case.pbl.git',
                                               credentialsId: GITHUB_CREDENTIALS]]])  // ID de credenciais atualizado
            }
        }

        stage('Build') {
            steps {
                echo 'Compilando o projeto...'
                sh 'mvn -B -DskipTests clean package'  // Compila o projeto sem executar os testes
            }
        }

        stage('Test') {
            steps {
                echo 'Executando testes...'
                sh 'mvn test'  // Executa os testes
            }
            post {
                always {
                    echo 'Coletando resultados de testes...'
                    junit 'target/surefire-reports/*.xml'  // Publica relatórios de testes no Jenkins
                }
            }
        }

        stage('Code Coverage') {
            steps {
                echo 'Gerando relatório de cobertura de código...'
                sh 'mvn jacoco:report'  // Executa o plugin JaCoCo para gerar o relatório de cobertura
            }
            post {
                always {
                    echo 'Publicando cobertura de código...'
                   publishHTML(target: [
                       allowMissing: false,
                       alwaysLinkToLastBuild: true,
                       keepAll: true,
                       reportDir: 'target/spotbugs',
                       reportFiles: 'spotbugs.html',
                       reportName: 'SpotBugs Analysis'
                   ])
                }
            }
        }

        stage('Static Code Analysis') {
            steps {
                echo 'Analisando o código com SpotBugs...'
                sh 'mvn spotbugs:spotbugs'  // Executa o plugin SpotBugs para análise de código
            }
            post {
                always {
                    echo 'Publicando relatório de análise de código...'
                    publishHTML(target: [
                        allowMissing: false,
                        alwaysLinkToLastBuild: true,
                        keepAll: true,
                        reportDir: 'target/spotbugs',
                        reportFiles: 'spotbugs.html',
                        reportName: 'SpotBugs Analysis'
                    ])
                }
            }
        }

        stage('Deploy') {
            when {
                branch 'main'  // Executa o deploy somente na branch main
            }
            steps {
                echo 'Realizando deploy da aplicação...'
                sh './jenkins/scripts/deploy.sh'  // Substitua pelo caminho correto do script de deploy
            }
        }
    }

    post {
        success {
            echo 'Pipeline executado com sucesso!'
        }
        failure {
            echo 'Pipeline falhou.'
        }
    }
}
