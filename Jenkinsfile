pipeline {
    agent none

    stages {
        stage('CLONE GIT REPOSITORY') {
            agent {
                label 'ubuntu-us-app'
            }
            steps {
                checkout scm
            }
        }

    stage('SCA-SAST-SNYK-TEST') {
        agent any
        steps {
            script {
                snykSecurity(
                    snykInstallation: 'Snyk',
                    snykTokenId: 'snyk_credentials',
                    severity: 'critical'
                )
            }
        }
    }

    stage('SonarQube Analysis') {
        agent {
            label 'ubuntu-us-app'
        }
        steps {
            script {
                def scannerHome = tool 'SonarQubeScanner'
                withSonarQubeEnv('sonarqube') {
                    sh "${scannerHome}/bin/sonar-scanner \
                    -Dsonar.projectKey=game-app \
                    -Dsonar.sources=."
                }
            }
        }
    }

    stage('BUILD-AND-TAG') {
        agent {
            label 'ubuntu-us-app'
        }
        steps {
            script {
                def app = docker.build("blakemfordham/chat-app")
                app.tag("latest")
            }
        }
    }

    stage('POST-TO-DOCKERHUB') {
        agent {
            label 'ubuntu-us-app'
        }
        steps {
            script {
                docker.withRegistry('https://registry.hub.docker.com', 'dockerhub_credentials') {
                    def app = docker.image("blakemfordham/chat-app")
                    app.push("latest")
                }
            }
        }
    }

    stage('DEPLOYMENT') {
        agent {
            label 'ubuntu-us-app'
        }
        steps {
            sh "docker-compose down"
            sh "docker-compose up -d"
        }
    }
}
}