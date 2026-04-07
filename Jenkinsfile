pipeline {
    agent any

    stages {
        stage("clonning repo") {
            steps {
                git branch: 'main', url: 'https://github.com/lalit192977/mini_finance.git'
            }
        }

        stage("build image") {
            steps {
                sh '''
                IMAGE="${JOB_NAME}:${BUILD_NUMBER}"
                docker build -t $IMAGE .
                '''
            }
        }

        stage("push to dockerhub") {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh '''
                    IMAGE="${JOB_NAME}:${BUILD_NUMBER}"
                    DOCKER_IMAGE="$USER/${JOB_NAME}:${BUILD_NUMBER}"

                    docker login -u $USER -p $PASS
                    docker tag $IMAGE $DOCKER_IMAGE
                    docker push $DOCKER_IMAGE
                    '''
                }
            }
        }

        stage("run container") {
            steps {
                sh '''
                IMAGE="${JOB_NAME}:${BUILD_NUMBER}"
                C_NAME="${JOB_NAME}"

                docker stop "$C_NAME" || true
                docker rm "$C_NAME" || true

                docker run -d --name "$C_NAME" -p 9333:80 "$IMAGE"
                '''
            }
        }
    }
}