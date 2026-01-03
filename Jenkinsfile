pipeline {
    agent any
tools{
    Maven Maven '3.9.12'
}
    stages {
        stage('build') {
            steps {
                echo 'building '
                sh 'mvn complie'
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
                sh 'mvn package -DskipTests'
            }
        }
    }

    post {
        always {
            echo 'This pipeline is completed..'
        }
    }
}
