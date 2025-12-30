pipeline {

    agent none

    tools {
        maven 'Maven 3.9.12'
    }

    stages {

        stage('Build') {
            agent any
            steps {
                script {
                    docker.image('maven:3.9.6-eclipse-temurin-17-alpine').inside {
                        sh 'mvn -B compile'
                    }
                }
            }
        }

        stage('Test') {
            agent any
            steps {
                script {
                    docker.image('maven:3.9.6-eclipse-temurin-17-alpine').inside {
                        sh 'mvn -B test'
                    }
                }
            }
        }

        stage('Package') {
            when { branch 'main' }
            parallel {

                stage('Maven Package') {
                    agent any
                    steps {
                        script {
                            docker.image('maven:3.9.6-eclipse-temurin-17-alpine').inside {

                                sh '''
                                GIT_SHORT_COMMIT=$(echo $GIT_COMMIT | cut -c1-7)
                                mvn versions:set -DnewVersion="$GIT_SHORT_COMMIT"
                                mvn versions:commit
                                mvn -B package -DskipTests
                                '''
                                archiveArtifacts '**/target/*.jar'
                            }
                        }
                    }
                }

                stage('Docker Build & Push') {
                    agent any
                    steps {
                        script {

                            docker.withRegistry('https://index.docker.io/v1/', 'dockerlogin') {
                                def commitHash = env.GIT_COMMIT.take(7)
                                def img = docker.build("initcron/sysfoo:${commitHash}", ".")

                                img.push()
                                img.push("latest")
                                img.push("dev")
                            }

                        }
                    }
                }

            }
        }
    }

    post {
        always {
            echo "Pipeline Completed."
        }
    }
}
