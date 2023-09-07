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

        stage('Checkstyle Analysis'){
            steps {
                sh 'mvn checkstyle:checkstyle'
            }
        }

        stage('Sonar Analysis') {
            environment {
                scannerHome = tool 'sonar4.7'
            }
            steps {
                withSonarQubeEnv('sonar') {
                    sh '''${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=vprofile \
                    -Dsonar.projectName=vprofile \
                    -Dsonar.projectVersion=1.0 \
                    -Dsonar.sources=src/ \
                    -Dsonar.java.binaries=target/test-classes/com/visualpathit/account/comtrollerTest/ \
                    -Dsonar.junit.reportsPath=target/surefire-reports/ \
                    -Dsonar.jacoco.reportsPath=target/jacoco.exec \
                    -Dsonar.java.checkstyle.reportPaths=target/checkstyle-result.xml'''
                }

            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 1, unit: 'HOURS') {
                    // Parameter indicates whether to set pipeline to UNSTABLE if Quality Gate fails
                    // true = set pipeline to UNSTABLE, false = don't
                    waitforQualityGate abortPipeline: True
                }
            }
        }

        stage ('UploadArtifact') {
            steps {
               nexusArtifactUploader(
                 nexusVersion: 'nexus3',
                 protocol: 'http',
                 nexusUrl: 'nexusserverprivateip',
                 groupId: 'QA',
                 version: "${env.BUILD_ID}-${env.BUILD_TIMESTAMP}",
                 repository: 'nameofnexusrepo',
                 credentialsId: 'nexusID',
                 artifacts: [
                    [artifactId: 'vproapp',
                     classifier: '',
                     file: 'target/vprofile-v2.war',
                     type: 'war']
                 ]
               )
            }
        }
    }
}
