pipeline {
    agent any

    tools {
        maven 'Maven'
        jdk 'JDK11'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh 'mvn compile -DskipTests'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    junit '**/target/surefire-reports/*.xml'
                }
            }
        }

        stage('JaCoCo') {
            steps {
                jacoco(
                    execPattern: '**/target/jacoco.exec',
                    classPattern: '**/target/classes',
                    sourcePattern: '**/src/main/java',
                    exclusionPattern: '**/src/test/**'
                )
            }
        }

        stage('PMD') {
            steps {
                sh 'mvn pmd:pmd'
            }
        }

        stage('Javadoc') {
            steps {
                sh 'mvn javadoc:javadoc'
            }
        }

        stage('Site') {
            steps {
                sh 'mvn site'
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: '**/target/*.jar, **/target/*.war, target/site/**', allowEmptyArchive: true
            publishHTML(target: [
                allowMissing: true,
                alwaysLinkToLastBuild: true,
                keepAll: true,
                reportDir: 'target/site',
                reportFiles: 'index.html',
                reportName: 'Site Documentation'
            ])
        }
    }
}
