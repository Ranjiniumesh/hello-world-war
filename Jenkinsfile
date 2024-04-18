pipeline {
    agent { label 'slave101' }

    stages {
        stage('checkout') {
            steps {
                sh 'rm -rf hello-world-war'
                sh 'git clone https://github.com/Ranjiniumesh/hello-world-war.git'
            }
        }
        
        stage('build') {
            steps {
                dir("hello-world-war") {
                    sh 'echo "inside build"'
                    sh "docker build -t tomcat-war:${BUILD_NUMBER} ."
                }
            }
        }
        
        stage('push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dckr_pat_WX3i3sDnG4yzNdEHYkC3cjlLGwI', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                    sh "docker login -u ${DOCKER_USERNAME} -p ${DOCKER_PASSWORD}"
                    sh "docker tag tomcat-war:${BUILD_NUMBER} shivaranjini4234/tomcat:${BUILD_NUMBER}"
                    sh "docker push shivaranjini4234/tomcat:${BUILD_NUMBER}"
                }
            }
        }
        
        stage('deploy') {
            parallel {
                stage('deployQA') {
                    agent { label 'slave102' }
                    steps {
                        script {
                            withCredentials([usernamePassword(credentialsId: 'dckr_pat_WX3i3sDnG4yzNdEHYkC3cjlLGwI', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                                sh "docker login -u ${DOCKER_USERNAME} -p ${DOCKER_PASSWORD}"
                                sh "docker pull shivaranjini4234/tomcat:${BUILD_NUMBER}"
                                sh 'docker rm -f tomcat-qa || true'
                                sh 'docker run -d -p 5555:8080 --name tomcat-qa shivaranjini4234/tomcat:${BUILD_NUMBER}'
                            }
                        }
                    }
                }
                stage('deployProd') {
                    agent { label 'slave33' }
                    steps {
                        script {
                            withCredentials([usernamePassword(credentialsId: 'dckr_pat_WX3i3sDnG4yzNdEHYkC3cjlLGwI', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                                sh "docker login -u ${DOCKER_USERNAME} -p ${DOCKER_PASSWORD}"
                                sh "docker pull shivaranjini4234/tomcat:${BUILD_NUMBER}"
                                sh 'docker rm -f tomcat-prod || true'
                                sh 'docker run -d -p 5555:8080 --name tomcat-prod shivaranjini4234/tomcat:${BUILD_NUMBER}'
                            }
                        }
                    }
                }
            }
        }
    }
}
