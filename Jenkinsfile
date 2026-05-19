pipeline {
    agent any

    tools {
        maven 'Maven'
        jdk 'JDK11'
    }

    stages {
        stage('Clean') {
            steps {
                sh 'mvn clean'
            }
        }
        stage('Compile') {
            steps {
                sh 'mvn compile'
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test -Dmaven.test.failure.ignore=true -Dtest="!TestPdfFormatHandler"'
            }
        }
        stage('PMD') {
            steps {
                sh 'mvn pmd:pmd'
            }
        }
        stage('JaCoCo') {
            steps {
                sh 'mvn jacoco:report'
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
        stage('Package') {
            steps {
                sh 'mvn -B -DskipTests clean package'
            }
        }
        stage('Building image') {
            steps {
                sh 'docker build -t brightonxx/teedyjenkins:v1.0 .'
            }
        }
        stage('Upload image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                    sh 'docker push brightonxx/teedyjenkins:v1.0'
                }
            }
        }
        stage('Run containers') {
            steps {
                sh 'docker rm -f teedy_manual01 teedy_manual02 teedy_manual03 2>/dev/null || true'
                sh 'docker run -d -p 8084:8080 --name teedy_manual01 brightonxx/teedyjenkins:v1.0'
                sh 'docker run -d -p 8083:8080 --name teedy_manual02 brightonxx/teedyjenkins:v1.0'
                sh 'docker run -d -p 8082:8080 --name teedy_manual03 brightonxx/teedyjenkins:v1.0'
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: '**/target/site/**/*.*', fingerprint: true
            archiveArtifacts artifacts: '**/target/**/*.jar', fingerprint: true
            archiveArtifacts artifacts: '**/target/**/*.war', fingerprint: true
            junit '**/target/surefire-reports/*.xml'
        }
    }
}
