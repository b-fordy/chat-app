node('ubuntu-us-app')
{
    
def app
stage('Cloning Git')
{
    checkout scm
}

stage('SCA-SAST-SNYK')
{
    snykSecurity(
        snykInstallation: 'Snyk',
        snykTokenId: 'snyk_credentials',
        severity: 'critical' 
    )
}

stage('SonarQube Analysis')
{
    agent {
        label 'ubuntu-us-app'
    }
    steps {
        script {
            def scannerHome = tool 'SonarQubeScanner'
            withSonarQubeEnv('sonarqube') {
                sh "${scannerHome}/bin/sonar-scanner \
                    -Dsonar.projectKey=game-app \
                    -Dsonar.source=."
            }
        }
    }
}

stage('Build-and-Tag')
{
    app = docker.build('blakemfordham/chat-app')
}

stage('Post-to-DockerHub')
{
    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub_credentials')
    {
        app.push('latest')
    }
} 

stage('Deploy')
{
    sh "docker-compose down"
    sh "docker-compose up -d"
} 
}