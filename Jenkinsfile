pipeline {
    agent any

    stages {

        stage ('Checkout') {
            steps {
                // echo "Running build ${env.BUILD_ID} on ${env.JENKINS_URL}"
                // echo 'Checkout source ...'
                checkout scm
            }
        }
    
        stage ('Build') {
            steps {
                sh './gradlew clean build -x test'
            }
        }

        stage ('Code Coverage Test') {
            parallel {
                stage ('Unit Test') {
                    steps {
                        sh './gradlew test'
                        // archiveUnitTestResults()
                    }
                }
                stage ('Sonarcube') {
                    steps {
                        echo 'Sonarqube tests'
                        sh 'sleep 10s'
                        // archiveCodeCoverageResults()
                    }
                }
            }
        }
    
        stage ('Integration Test') {
            steps {
                echo 'Here goes the integration tests'
                sh 'sleep 10s'
            }
        }
    
        stage ('Scale Test') {
            steps {
                echo 'Here goes the scale tests'
                sh 'sleep 10s'
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
