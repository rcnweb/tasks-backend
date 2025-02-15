pipeline{
    agent any
    stages{
        stage('Build Backend'){
            steps{
                sh 'mvn clean package -DskipTests=true'
            }
        }
        stage('Unit Tests'){
            steps{
                sh 'mvn test'
            }
        }
        stage('Sonar Analysis'){
            environment{
                scannerHome = tool 'SONAR_SCANNER'
            }
            steps{
                withSonarQubeEnv('SONAR_LOCAL'){
                    sh "${scannerHome}/bin/sonar-scanner -e -Dsonar.projectKey=DeployBack -Dsonar.host.url=http://localhost:9000  -Dsonar.login=eb614958387b2a40e60196f5262006ceceef8724 -Dsonar.java.binaries=target"
                }
            }
        }
        stage('Deploy Backend'){
            steps{
                deploy adapters: [tomcat8(credentialsId: 'TomcatLogin', path: '', url: 'http://localhost:8001')], contextPath: 'tasks-backend', war: 'target/tasks-backend.war'
            }
        }
        stage('API Test'){
            steps{
                dir('api-test'){
                    git credentialsId: 'github_login', url: 'https://github.com/rcnweb/tasks-api-test'
                    sh 'mvn test'
                }
            }
        }
        stage('Deploy Frontend'){
            steps{
                dir('frontend'){
                    git credentialsId: 'github_login', url: 'https://github.com/rcnweb/tasks-frontend'
                    sh 'mvn clean package'
                    deploy adapters: [tomcat8(credentialsId: 'TomcatLogin', path: '', url: 'http://localhost:8001')], contextPath: 'tasks', war: 'target/tasks.war'
                }
                
            }
        }
        stage('Functional Test'){
            steps{
                dir('functional-test'){
                    git credentialsId: 'github_login', url: 'https://github.com/rcnweb/tasks-functional-test'
                    sh 'mvn test'
                }
            }
        }
        stage('Deploy Prod'){
            steps{
                sh 'docker-compose build'
                sh 'docker-compose up -d'
            }
        }
        stage('Health Check'){
            steps{
                sleep(10)
                dir('functional-test'){
                    sh 'mvn verify -Dskip.surefire-tests'
                }
            }
        }
    }
}


