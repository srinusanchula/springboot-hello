pipeline {
    agent any

    stages {

        stage ('Checkout') {
            steps {
                // echo "Running build ${env.BUILD_ID} on ${env.JENKINS_URL}"
                echo 'Checkout source'
                checkout scm
            }
        }
    
        stage ('Build') {
            steps {
                echo 'Build source'
                sh './gradlew clean build -x test'
            }
        }

        stage ('Functional Test') {
            parallel {
                stage ('Static Analysis') {
                    steps {
                        echo 'Sonarqube tests'
                        sh 'sleep 10s'
                        // archiveCodeCoverageResults()
                    }
                }
                stage ('Unit/Mock Test') {
                    steps {
                        echo 'Unit/Mock testing'
                        sh './gradlew test jacocoTestReport'
                        // archiveUnitTestResults()
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
