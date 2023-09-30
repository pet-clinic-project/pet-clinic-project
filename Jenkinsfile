@Library('jenkins-shared-library@develop') _

def awsRegion = "us-west-2"
def imageName = "java_application"
def versionTag = "1.0.0"
def emailRecipient = "aswin@crunchops.com"
def gitUrl = "https://github.com/spring-projects/spring-petclinic.git"
def gitBranch = "main"

pipeline {
    agent {
        docker { image '814200988517.dkr.ecr.us-west-2.amazonaws.com/base-image:1.0.0' }
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
        stage('Upload to Nexus') {
            steps {
                script {
                    nexusUpload(
                        'http://34.217.101.50:8081',
                        'repository/maven-releases/jarfile/pet-clinic/' + versionTag,
                        'NEXUS_CRED',
                        'pet-clinic-' + versionTag + '.jar')
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