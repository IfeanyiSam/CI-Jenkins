pipeline {
    agent any
    tools {
        maven "MAVEN3"
        jdk "OracleJDK11"
    }

    Stages {
        stage('Fetch Code') {
            steps {
                git branch: 'main', url: 'https://github.com/IfeanyiSam/vprofile-project.git'
            }
        }
        
        stage('Build'){
            steps{
                sh 'mvn install -DskipTests'
            }

            post {
                success {
                    echo 'Archiving artifacts now.'
                    archiveArtifacts artifacts: '**/*.war'
                }
            }
        }
        stage('UNIT TESTS') {
            steps {
                sh 'mvn test'
            }
        }
    }
}