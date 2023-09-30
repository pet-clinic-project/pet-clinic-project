@Library('jenkins-shared-library@develop') _

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
                        gitUrl: "https://github.com/spring-projects/spring-petclinic.git",
                        branch: "main",
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
                        'SONAR_TOKEN')
                }
            }
        }
        stage('Upload to Nexus') {
            steps {
                script {
                    nexusUpload(
                        "repository/maven-releases/jarfile/pet-clinic/1.0.${BUILD_NUMBER}",
                        "NEXUS_CRED",
                        "pet-clinic-1.0.${BUILD_NUMBER}.jar")
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