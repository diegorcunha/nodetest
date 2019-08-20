pipeline {
    agent {
        kubernetes {
            cloud 'kubernetes'
            label 'node-slave'
            defaultContainer 'node-slave'
        }
    }

    stages {
        stage('Checkout') {
            steps {
                script {
                    scmVars = checkout scm
                    version = "${scmVars.GIT_COMMIT}"
                    dockerTag = "${env.BRANCH_NAME}-${scmVars.GIT_COMMIT}"
                    brench = "${env.BRANCH_NAME}"
                }
            }
        }



        stage('Build') {
            steps {
                container(name: "docker-slave") {
                    script {
                        echo "Build Image"
                        apiImage = docker.build("future-force-248018/node-test:$dockerTag", " --network=host .")
                    }
                }
            }
        }


        stage('Upload') {
            steps {
                container(name: "docker-slave") {
                    script {
                        if ("$brench" == "master") {
                            writeFile file: 'deploy.properties', text: "image=$dockerTag"
                            archiveArtifacts 'deploy.properties'
                            docker.withRegistry('https://us.gcr.io', 'gcr:node-test') {
                                docker.image("future-force-248018/node-test:$dockerTag").push("$dockerTag")
                            }
                        } else {
                            writeFile file: 'deploy.properties', text: "image=$dockerTag"
                            archiveArtifacts 'deploy.properties'
                            docker.withRegistry('https://us.gcr.io', 'gcr:node-test') {
                                docker.image("future-force-248018/node-test:$dockerTag").push("$dockerTag")
                            }
                        }
                    }
                }
            }
        }
    }

//    post {
//        success {
//            slackSend(color: '#00FF00', message: "SUCCESSFUL: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
//        }
//
//        failure {
//            slackSend(color: '#FF0000', message: "FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
//        }
//    }
}
