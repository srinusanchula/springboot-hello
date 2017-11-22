props = null

def init() {
    node {
        checkout scm
        properties = new Properties()
        File propertiesFile = new File("${workspace}/uem-pipeline.properties")
        props.load(propertiesFile.newDataInputStream())
    }
}


pipeline {
    agent any

    stages {

        stage ('Checkout') {
            steps {
                echo "Checkout source"
                script {
                    init()
                    echo "Init successful for project ${props.PROJECT}"
                }
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
                echo "Build docker image"
                sh 'sleep 5s'
                echo "Push docker image"
                sh 'sleep 5s'
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
