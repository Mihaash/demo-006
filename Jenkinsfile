pipeline {
  agent any
  stages {
    stage('build') {
      steps {
        echo 'building '
        sh 'mvn compile'
      }
    }

    stage('test') {
      steps {
        echo 'testing'
        sh 'mvn clean test '
      }
    }

    stage('package') {
      steps {
        echo 'package'
        sh '''# Truncate the GIT_COMMIT to the first 7 characters
GIT_SHORT_COMMIT=$(echo "$GIT_COMMIT" | cut -c 1-7)

# Set the version using Maven (NO SPACE)
mvn versions:set -DnewVersion="$GIT_SHORT_COMMIT"

# Commit the version change
mvn versions:commit
'''
        sh 'mvn package -DskipTests'
        archiveArtifacts '**/target/*.jar'
      }
    }

  }
  tools {
    maven 'Maven 3.9.12'
  }
  post {
    always {
      echo 'This pipeline is completed..'
    }

  }
}