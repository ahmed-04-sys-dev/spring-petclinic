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
                        bat 'mvn jacoco:prepare-agent verify jacoco:report'
                    }
                    post {
                        always {
                            publishHTML(target: [
                                allowMissing         : true,
                                alwaysLinkToLastBuild: true,
                                keepAll              : true,
                                reportDir            : 'target/site/jacoco',
                                reportFiles          : 'index.html',
                                reportName           : 'Code Coverage JaCoCo'
                            ])
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
                        allowMissing         : true,
                        alwaysLinkToLastBuild: true,
                        keepAll              : true,
                        reportDir            : 'target/site',
                        reportFiles          : 'index.html',
                        reportName           : 'Maven Site'
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
            echo "Pipeline échoué - vérifiez les logs"
        }
        success {
            echo 'Pipeline terminé avec succès !'
        }
    }
}
