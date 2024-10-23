node('ubuntu-us-app')
{
    
def app
stage('Cloning Git')
{
    checkout scm
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