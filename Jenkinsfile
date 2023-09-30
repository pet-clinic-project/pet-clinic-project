@Library('jenkins-shared-library@develop') _

def emailRecipient = "aswin@crunchops.com"
def gitUrl = "https://github.com/spring-projects/spring-petclinic.git"
def gitBranch = "main"

pipeline {
    agent {
        docker {
            image '814200988517.dkr.ecr.us-west-2.amazonaws.com/base-image:java-jdk-17-agent-1.0.22'
            args '-v $HOME/.m2:/root/.m2 -u root'
            }
    }

    stages {
        stage('Git Checkout') {
            steps {
                script {
                    def config = [
                        gitUrl: "${gitUrl}",
                        branch: "${gitBranch}",
                    ]
                    gitCheckout.call(config)
                }
            }
        }
        stage('Build') {
            steps {
                script {
                    mvnBuild()
                }
            }
        }
        stage('SonarQube Scan') {
            steps {
                script {
                    sonarScan(
                        'sonarqube-petclinic_java-application',
                        'sonarqube-petclinic',
                        'https://sonarcloud.io',
                        'SONAR_TOKEN')
                }
            }
        }
    }
    post {
        always {
            cleanWs()
        }
    }
}