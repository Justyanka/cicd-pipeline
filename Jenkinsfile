pipeline {
    agent any

    tools {
        nodejs 'Default'
    }

    triggers {
        pollSCM('H/1 * * * *')
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
                    if (env.BRANCH_NAME == 'main' ) {
                       sh '''
                          docker build -t nodemain:v1.0 .
                          Containers=$(docker ps -a --filter "ancestor=nodemain:v1.0" -q)
                          if [ "$Containers" ]; then 
                              docker rm -f $Containers  
                          fi
                          docker run -d --expose 3000 -p 3000:3000 nodemain:v1.0
                       '''
                    } else if (env.BRANCH_NAME == 'dev') {
                       sh '''
                          docker build -t nodedev:v1.0 .
                          Containers=$(docker ps -a --filter "ancestor=nodedev:v1.0" -q)
                          if [ "$Containers" ]; then
                             docker rm -f $Containers
                          fi
                          docker run -d --expose 3001 -p 3001:3000 nodedev:v1.0
                       '''
                    }
                }
            }
        }
    }
}
