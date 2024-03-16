pipeline {
    agent { docker { image 'python:3.12.1-alpine3.19' } }

    options{
        buildDiscarder(logRotator(numToKeepStr: '5', daysToKeepStr: '5'))
        timestamps()
    }
    environment{

        registry = "vkunal/cncf-hoogly"
        registryCredential = 'dockerhub' 
        commitHash = sh(returnStdout: true, script: 'git rev-parse --short HEAD').trim()

    }

    stages {

        stage('Run Tests') {
            steps {
                script{
                    sh "python manage.py test -v=3 > test_reports/${commitHash}.txt"

                }
            }
        }
        stage('Build Docker Image') {
            steps {
                script{
                    sh "docker build -t ${registry}:${commitHash} ."

                }
            }
        }
        stage('Run Docker Container') {
            steps {
                script{
                    sh "docker run -dp 8000:8000 ${registry}:${commitHash}"

                }
            }
        }
        stage('Push to DockerHub') {
            steps {
                script{
                    docker.withRegistry( 'https://index.docker.io/v1/', registryCredential) {
                        sh "docker push ${registry}:${commitHash}"
                    }

                }
            }
        }
    }
}
