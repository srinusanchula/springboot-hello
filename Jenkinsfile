properties = null

def loadProperties() {
    node {
        uem_props = readProperties file: '../uem-pipeline.properties'
    }
}

pipeline {
    agent any

    stages {

        stage ('Checkout') {
            steps {
                echo "Load properties"
                loadProperties()
                echo "Properties loaded for ${uem_props.PROJECT}"
                echo "Checkout source"
                checkout scm
                echo "Checkout successful"
            }
        }
    
        stage ('Build') {
            steps {
                echo "Building source, Running build ${env.BUILD_ID}."
                sh "./gradlew clean build -x test -PBUILD_ID=${env.BUILD_ID}"
            }
        }

        stage ('Functional Test') {
            parallel {
                stage ('Static Analysis') {
                    steps {
                        echo "Sonarqube tests"
                        sh 'sleep 10s'
                        // archiveCodeCoverageResults()
                    }
                }
                stage ('Unit/Mock Test') {
                    steps {
                        echo "Unit/Mock testing"
                        sh "./gradlew test jacocoTestReport -PBUILD_ID=${env.BUILD_ID}"
                        // archiveUnitTestResults()
                    }
                }
            }
        }
    
        stage ('Containerization') {
            steps {
                echo "Clenup docker images"
                sh "docker images -a | sed \"1 d\" | grep -v java | xargs docker rmi"
                echo "Build docker image"
                sh "./gradlew dockerBuildImage -PBUILD_ID=${env.BUILD_ID}"
                echo "Push docker image"
                sh "./gradlew dockerPushImage -PBUILD_ID=${env.BUILD_ID} -PACR_LOGIN_SERV=${uem_props.ACR_LOGIN_SERV} -PACR_USERNAME=${uem_props.ACR_USERNAME} -PACR_PASSWORD=${uem_props.ACR_PASSWORD}"
            }
        }

        stage ('Deploy Azure Approval') {
            steps {
                echo "New build image hello:${env.BUILD_ID} is ready. Please complete the DAP, JAF and Scale tests."

                input 'Deploy to azure staging?'
            }
        }

        stage ('Deploy Azure') {
            steps {
                echo 'Here goes the azure staging deployment'
                sh 'sleep 10s'
            }
        }
    }
}

def archiveUnitTestResults() {
    step([$class: "JUnitResultArchiver", testResults: "build/**/TEST-*.xml"])
}

def archiveCodeCoverageResults() {
    step([$class: "CodeCoveragePublisher",
          canComputeNew: false,
          defaultEncoding: "",
          healthy: "",
          pattern: "build/reports/sonarq/main.xml",
          unHealthy: ""])
}
