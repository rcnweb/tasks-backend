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
        stage('Quality Gate'){
            steps{
                sleep(40)
                timeout(time: 1, unit:'MINUTES'){
                    waitForQualityGate abortPipeline:true
                }
            }
        }
    }
}


