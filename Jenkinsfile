pipeline {
    agent any
    tools {
        maven 'Maven_3_8_4'
    }
    stages{
    stage('CompileandRunSonarAnalysis') {
            steps {
                sh 'mvn clean verify org.sonarsource.scanner.maven:sonar-maven-plugin:sonar -Dsonar.projectKey=captainbanks_captainbanks -Dsonar.organization=captainbanks -Dsonar.host.url=https://sonarcloud.io -Dsonar.token=a7cc86c2e8571e575855af67e2bd7eb3c718e878'
            }
        }

            stage('RunSCAAnalysisUsingSnyk') {
            steps {
                withCredentials([string(credentialsId: 'SNYK_TOKEN', variable: 'SNYK_TOKEN')]) {
                    sh 'mvn snyk:test -fn'
                }
            }
        }

        stage('Build') {
            steps {
                withDockerRegistry([credentialsId: "dockerlogin", url: ""]) {
                    script {
                        app = docker.build("asg")
                    }
                }
            }
        }

        stage('Push') {
            steps {
                script {
                    docker.withRegistry('https://051370879805.dkr.ecr.us-east-1.amazonaws.com', 'ecr:us-east-1:aws-credentials') {
                        app.push("latest")
                    }
                }
            }
        }
    }
}
