properties([
  [$class: 'GithubProjectProperty', displayName: 'Release Demo', projectUrlStr: 'https://github.com/jenkins-demo/release-demo/']
])

pipeline {
  
  options {
    buildDiscarder(logRotator(artifactNumToKeepStr: '5', numToKeepStr: '10'))
  }
  agent {
    docker { image 'cloudbees/java-build-tools:2.5.0' }
  }
  
  stages {
    stage('Prepare') {
      steps {
        script {
          def commitId = sh(returnStdout: true, script: 'git rev-parse --verify --short HEAD')?.trim()
          def branchName = env.BRANCH_NAME?.trim()?.replaceAll('/', '_')

          def imageTag = "${branchName}-${commitId}-${env.BUILD_ID}"
          currentBuild.description = "Release Demo ${imageTag}"
          imageName = "jenkins-demo/release-demo:${imageTag}"
        }
      }
    }

    stage('Build') {
      steps {
        mvn "verify"
      }
    }

    stage('Release') {
      when {
        branch 'develop'
      }
      steps {
        mvn "gitflow:release"
      }
    }
  }
}

// Utility functions
def mvn(String goals) {
  withMaven(
    mavenOpts: '-Xmx768m -Djava.awt.headless=true'
  ){
    sh "\$MVN_CMD -B ${goals}"
  }
}
