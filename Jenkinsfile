pipeline {
    // agent {
    //     docker {
    //         image 'maven:3-alpine' 
    //         args '-v /root/.m2:/root/.m2'
    //     }
    // }
    //
    agent any
    stages {
        stage('Build') { 
            steps {
                withDockerContainer(args: '-v /root/.m2:/root/.m2', image: 'maven:3-alpine', toolName: 'myDocker')
                {
                    sh 'mvn -B -DskipTests clean package' 
                }
                
            }
        }
        stage('Test') {
            steps {
                withDockerContainer(args: '-v /root/.m2:/root/.m2', image: 'maven:3-alpine', toolName: 'myDocker')
                {
                    sh 'mvn test'
                }
                
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }
        stage('Sonar') {
            steps {
                def scannerHome = tool 'SonarScanner 4.0';
                withSonarQubeEnv('sonar-nantes') { // If you have configured more than one global server connection, you can specify its name
                    sh "${scannerHome}/bin/sonar-scanner"
                }
            }
            
        }
        stage('Deliver') {
            steps {
                 withDockerContainer(args: '-v /root/.m2:/root/.m2', image: 'maven:3-alpine', toolName: 'myDocker')
                {
                    sh './jenkins/scripts/deliver.sh'
                }
            }
        }
    }
}