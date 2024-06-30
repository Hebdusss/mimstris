pipeline {
    agent any

    triggers {
        pollSCM("H/5 * * * *")
    }

    stages {
        stage("Verify tooling") {
            steps {
                sh "docker version" 
                sh "docker info"
            }
        }
        stage("Build") {
            steps {
                git branch: "master", url: "https://github.com/Hebdusss/mimstris.git"

                script {
                    def dockerBuildOutput = sh(script: "docker image build -t Hebdusss/react-tetris:latest .", returnStatus: true)
                    if(dockerBuildOutput == 0) {
                        currentBuild.result = "SUCCESS"
                    } else {
                        currentBuild.result = "FAILURE"
                    }
                }
            }
            post {
                success {
                    echo "Build stage success"
                }
                failure {
                    echo "Build stage failure"
                }
            }
        }

        stage("Test") {
            steps {
                script {
                    def testResult = sh(script: "docker image build -t Hebdusss/react-tetris-tests -f DockerfileTest . && docker run --name tests Hebdusss/react-tetris-tests", returnStatus: true)
                    
                    if(testResult == 0) {
                        currentBuild.result = "SUCCESS"
                    } 
                    else {
                        currentBuild.result = "FAILURE"
                    }
                }
            }
            post {
                always {
                    echo "Tests finished"
                }
                success {
                    echo "Testing stage success"
                }
                failure {
                    echo "Testing stage failure"
                }
            }
        }
        
        stage("Deploy") {
            steps {
                script {
                    def dockerRun = "docker run --name app -d -p 5173:5173 Hebdusss/react-tetris"
                    def dockerRunOutput = sh(script: dockerRun, returnStdout: true).trim()

                    if(dockerRunOutput) {
                        echo "Container run success: ${dockerRunOutput}"
                        currentBuild.result = "SUCCESS"
                    } 
                    else {
                        error "Container run failure"
                        currentBuild.result = "FAILURE"
                    }
                }
            }
            post {
                success {
                    echo "Deploy stage success"
                }
                failure {
                    echo "Deploy stage failure"
                }
            }
        }
    }
}