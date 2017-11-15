pipeline {
    agent any

    stages {

        stage ('Checkout') {
            echo "Running build ${env.BUILD_ID} on ${env.JENKINS_URL}"
            echo 'Checkout source ...'
            checkout scm
        }
    
        stage ('Build') {
            sh './gradlew clean build -x test'
        }

        stage ('Code Coverage Test') {
            parallel {
                stage ('Unit Test') {
                    sh './gradlew test'
                    // archiveUnitTestResults()
                }
                stage ('Sonarcube') {
                    echo 'Sonarqube tests'
                    sh 'sleep 10s'
                    // archiveCodeCoverageResults()
                }
            }
        }
    
        stage ('Integration Test') {
            echo 'Here goes the integration tests'
            sh 'sleep 10s'
        }
    
        stage ('Scale Test') {
            echo 'Here goes the scale tests'
            sh 'sleep 10s'
        }

        stage ('Deploy Azure') {
            echo 'Here goes the azure staging deployment'
            sh 'sleep 10s'
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
