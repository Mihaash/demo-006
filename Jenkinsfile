pipeline {
    agent none

    tools {
        maven 'Maven 3.9.12'
    }

    stages {

        stage('Build') {
            agent {
                dockerContainer {
                    image 'maven:3.9.6-eclipse-temurin-17-alpine'
                }
            }
            steps {
                echo "Compiling..."
                sh "mvn -B -DskipTests clean compile"
            }
        }

        stage('Test') {
            agent {
                dockerContainer {
                    image 'maven:3.9.6-eclipse-temurin-17-alpine'
                }
            }
            steps {
                echo "Running tests..."
                sh "mvn -B test"
            }
        }

        stage('Package & Docker') {
            when { branch 'main' }

            parallel {

                stage('Maven Package') {
                    agent {
                        dockerContainer {
                            image 'maven:3.9.6-eclipse-temurin-17-alpine'
                        }
                    }
                    steps {
                        echo "Packaging JAR..."
                        sh "mvn -B -DskipTests package"
                        archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
                    }
                }

                stage('Docker Build & Push') {
                    agent any
                    steps {
                        script {
                            def tag = env.GIT_COMMIT.take(7)
                            echo "Using Docker Tag: ${tag}"

                            docker.withRegistry('https://index.docker.io/v1/', 'dockerlogin') {
                                def img = docker.build("initcron/sysfoo:${tag}")
                                img.push()
                                img.push("latest")
                            }
                        }
                    }
                }

            }
        }
    }

    post {
        always {
            echo "Pipeline Completed"
        }
    }
}
