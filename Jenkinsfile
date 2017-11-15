node("linux && jdk8") {
    stage "Checkout"
    git url: "https://github.com/srinusanchula/springboot-hello.git"
    
    stage "Build"
    sh "./gradlew clean build -x test"

    stage "UnitTest/CodeCoverage"
    parallel (
      "Unit Tests": {
        sh "./gradlew test"
//      archiveUnitTestResults()
      },
      "Code Coverage": {
        echo 'Here goes the code coverage'
        sh 'sleep 30s'
//      archiveCodeCoverageResults()
      }
    )
    
    stage "Integration Test"
    echo 'Here goes the integration tests'
    sh 'sleep 10s'    
    
    stage "Scale Test"
    echo 'Here goes the scale tests'
    sh 'sleep 10s'

    stage name: "Deploy to Azure Staging", concurrency: 1
    echo 'Here goes the deployment'
    sh 'sleep 10s'
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
