pipeline {
    agent any

    tools {
        maven 'Maven 3.9.12'
        jdk 'jdk-21'
    }

    stages {

        stage('Build') {
            steps {
                bat 'mvn clean compile'
            }
        }

        stage('Tests') {
            parallel {

                stage('Tests Unitaires') {
                    steps {
                        bat 'mvn test'
                    }
                    post {
                        always {
                            junit 'target/surefire-reports/**/*.xml'
                        }
                    }
                }

                stage('Couverture de Code') {
                    steps {
                        bat 'mvn cobertura:cobertura -Dcobertura.report.format=xml'
                    }
                    post {
                        always {
                            cobertura coberturaReportFile: 'target/site/cobertura/coverage.xml',
                                      onlyStable: false
                        }
                    }
                }

                stage('Documentation') {
                    steps {
                        bat 'mvn javadoc:javadoc'
                    }
                }
            }
        }

        stage('Site & Rapport') {
            steps {
                bat 'mvn site -DskipTests'
            }
            post {
                always {
                    publishHTML(target: [
                        reportDir  : 'target/site',
                        reportFiles: 'index.html',
                        reportName : 'Maven Site'
                    ])
                }
            }
        }

        stage('Package') {
            steps {
                bat 'mvn package -DskipTests'
            }
            post {
                success {
                    archiveArtifacts artifacts: 'target/*.jar,target/*.war',
                                     fingerprint: true,
                                     allowEmptyArchive: true
                }
            }
        }
    }

    post {
        failure {
            mail to: 'admin@example.com',
                 subject: "ECHEC - ${JOB_NAME} #${BUILD_NUMBER}",
                 body: "Une étape a échoué.\nVoir : ${BUILD_URL}"
        }
        success {
            echo 'Pipeline terminé avec succès !'
        }
    }
}
