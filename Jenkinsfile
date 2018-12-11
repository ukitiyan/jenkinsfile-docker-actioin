pipeline {
    agent any
    parameters {
        string(name: 'DOCKER_IMAGE_NAME', defaultValue: 'ukitiyan/docker-action-example')
        string(name: 'DOCKER_IMAGE_TAG', defaultValue: 'latest')
        string(name: 'DOCKER_CONTAINER_NAME', defaultValue: 'docker-action')
        string(name: 'DOCKER_SRC_PORT', defaultValue: '8001')
        string(name: 'DOCKER_DEST_PORT', defaultValue: '8080')
        string(name: 'EXEC_PARAMETER', defaultValue: '')
    }
    stages {
        stage('CLEANUP') {
            when {
                expression {
                    dockerContainerInfo = sh(script: 'docker container ls', returnStdout: true)
                    for (container in dockerContainerInfo.split('\n')) {
                        if ("${container}" ==~ /(.*)${params.DOCKER_CONTAINER_NAME}/
                            && !("${container}" ==~ /(.*) ${params.DOCKER_IMAGE_NAME}:${params.DOCKER_IMAGE_TAG} (.*)/)) {
                            echo "${container}"
                            return true
                        }
                    }
                    return false
                }
            }
            steps {
                sh "docker container rm -f ${params.DOCKER_CONTAINER_NAME}"
            }
        }
        stage('PULL') {
            when {
                expression {
                    dockerImageInfo = sh(script: 'docker image ls', returnStdout: true)
                    for (image in dockerImageInfo.split('\n')) {
                        if ("${image}" ==~ /${params.DOCKER_IMAGE_NAME} (.*) ${params.DOCKER_IMAGE_TAG} (.*)/) {
                            echo "${image}"
                            return false
                        }
                    }
                    return true
                }
            }
            steps {
                sh "docker image pull ${params.DOCKER_IMAGE_NAME}:${params.DOCKER_IMAGE_TAG}"
            }
        }
        stage('RUN') {
            when {
                expression {
                    dockerContainerInfo = sh(script: 'docker container ls', returnStdout: true)
                    for (container in dockerContainerInfo.split('\n')) {
                        if ("${container}" ==~ /(.*)${params.DOCKER_CONTAINER_NAME}/) {
                            echo "${container}"
                            return false
                        }
                    }
                    return true
                }
            }
            steps {
                sh "docker container run --name docker-action -p ${params.DOCKER_SRC_PORT}:${params.DOCKER_DEST_PORT} -it --rm -d ${params.DOCKER_IMAGE_NAME}:${params.DOCKER_IMAGE_TAG}"
            }
        }
        stage('EXEC') {
            steps {
                sh "docker container exec -i ${params.DOCKER_CONTAINER_NAME} ./action/exec \"${params.EXEC_PARAMETER}\""
            }
        }
    }
}