pipeline {
    agent any

    tools {
        nodejs 'Default'
    }

    triggers {
        pollSCM('H/1 * * * *')
    }

    environment {
        DOCKERHUB_CREDENTIALS = credentials('justyanka-dokcerhub')
    }

    stages {
        stage('Build') {
            steps {
                sh 'npm install'
            }
        }
        stage('test') {
            steps {
                sh 'npm test'
            }
        }
        stage('build_docker_image') {
            steps {
                script {
                    sh 'echo ${DOCKERHUB_CREDENTIALS_PSW} | docker login -u ${DOCKERHUB_CREDENTIALS_USR} --pasword-stdin'
                    if (env.BRANCH_NAME == 'main' ) {
                       sh '''
                          docker build -t nodemain:v1.0 .
                          docker push nodemain:v1.0                          
                       '''
                       build job: 'Deploy_to_main', wait: false
                    } else if (env.BRANCH_NAME == 'dev') {
                       sh '''
                          docker build -t nodedev:v1.0 .
                          docker push nodedev:v1.0
                       '''
                       build job: 'Deploy_to_dev', wait: false
                    }
                    sh 'docker logout'
                }
            }
        }
    }
}
