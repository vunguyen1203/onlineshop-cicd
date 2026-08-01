pipeline {
    agent any

    environment {
        SONAR_PROJECT_KEY = "sonar.projectKey=onlineshop"

        DOCKER_HUB = "vucp2912n720"
        DOCKER_HUB_USER = credentials('dockerhub-user')
        NAME_BACKEND = "onlineshop-backend"
        NAME_FRONTEND = "onlineshop-frontend"
        DOCKER_TAG = "${GIT_BRANCH.tokenize('/').pop()}-${GIT_COMMIT.substring(0,7)}"

        RECIPIENT_EMAIL= "nguyenvuqn122003@gmail.com"
    }

    stages {
        stage('Sonarqube analysis') {
            steps {
                script {
                    def scannerHome = tool "SonarScanner"
                    withSonarQubeEnv('Sonarqube server connection') {
                        sh """
                            ${scannerHome}/bin/sonar-scanner \
                            -D${SONAR_PROJECT_KEY}
                        """
                    }
                }
            }
        }

        stage('Build and push images') {
            steps {
                script {
                    sh """
                        IMAGE_TAG=${DOCKER_TAG} \
                        DOCKER_HUB=${DOCKER_HUB} \
                        NAME_BACKEND=${NAME_BACKEND} \
                        NAME_FRONTEND=${NAME_FRONTEND} \
                        docker-compose build --parallel
                        
                        echo "${DOCKER_HUB_USER_PSW}" | docker login -u "${DOCKER_HUB_USER_USR}" --password-stdin 
                        docker push ${DOCKER_HUB}/${NAME_BACKEND}:${DOCKER_TAG} 
                        docker push ${DOCKER_HUB}/${NAME_FRONTEND}:${DOCKER_TAG} 
                        
                        docker rmi -f ${DOCKER_HUB}/${NAME_BACKEND}:${DOCKER_TAG} 
                        docker rmi -f ${DOCKER_HUB}/${NAME_FRONTEND}:${DOCKER_TAG}
                    """
                }
            }
        }

        stage('Deploy to server') {
            steps {
                script {
                    sshagent(credentials: ['jenkins-ssh-key']) {
                        sh """
                            ssh -o StrictHostKeyChecking=no onlineshop@192.168.117.23 "
                                docker rm -f ${NAME_BACKEND} ${NAME_FRONTEND} || true
                                docker run --name=${NAME_BACKEND} -dp 8080:80 ${DOCKER_HUB}/${NAME_BACKEND}:${DOCKER_TAG}
                                docker run --name=${NAME_FRONTEND} -dp 80:80 ${DOCKER_HUB}/${NAME_FRONTEND}:${DOCKER_TAG}
                            "
                        """
                    }
                }
            }
        }
    }

    post {
        success {
            mail to: "${RECIPIENT_EMAIL}",
                 subject: "SUCCESSFUL: Job '${JOB_NAME}' [Build #${BUILD_NUMBER}]",
                 body: """
                    Success! The pipeline for ${JOB_NAME} has completed.

                    - Build Number: #${BUILD_NUMBER}
                    - Branch: ${GIT_BRANCH}
                    - Commit: ${GIT_COMMIT}
                    - Build details at: ${BUILD_URL}
                 """
        }

        failure {
            mail to: "${RECIPIENT_EMAIL}",
                 subject: "FAILED: Job '${JOB_NAME}' [Build #${BUILD_NUMBER}]",
                 body: """
                    Failed! The pipeline for ${JOB_NAME} encountered an error.

                    - Build Number: #${BUILD_NUMBER}
                    - Branch: ${GIT_BRANCH}
                    - Commit: ${GIT_COMMIT}            
                    - Check the error log at: ${BUILD_URL}console
                 """
        }
    }
}