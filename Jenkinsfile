pipeline {
    environment {
        // This registry is important for removing the image after the tests
        registry = "dimaslz/nodejs-ts"
    }
    agent { docker { image 'node:14-alpine' } }
    stages {
        stage("Test") {
            steps {
                script {
                    // Building the Docker image
                    dockerImage = docker.build registry + ":$BUILD_NUMBER"

                    try {
                        dockerImage.inside() {
                            // Extracting the PROJECTDIR environment variable from inside the container
                            def PROJECTDIR = sh(script: 'echo \$PROJECTDIR', returnStdout: true).trim()

                            // Copying the project into our workspace
                            sh "cp -r '$PROJECTDIR' '$WORKSPACE'"

                            // Running the tests inside the new directory
                            dir("$WORKSPACE$PROJECTDIR") {
                                sh "yarn test"
                            }
                        }

                    } finally {
                        // Removing the docker image
                        sh "docker rmi $registry:$BUILD_NUMBER"
                    }
                }
            }
        }
    }
}