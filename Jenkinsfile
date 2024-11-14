pipeline{
    agent any
    tools {
        maven "MAVEN3"
        jdk "OracleJDK17"
    }
    environment {
        SNAP_REPO = 'vprofile-snapshot'
        NEXUS_USER = 'admin'
        NEXUS_PASS = 'admin'
        RELEASE_REPO = 'vprofile-release'
        CENTRAL_REPO = 'vpro-maven-central'
        NEXUSIP = '172.31.10.16'
        NEXUSPORT = '8081'
        NEXUS_GRP_REPO = 'vpro-maven-group'
        NEXUS_LOGIN = 'nexuslogin'
        SONARQUBE_SERVER ='SonarServer'
        SONARSCANNER ='SonnarQubeScanner'
        SONAR_TOKEN ='3f743330f95f281ca59065039bc592c199702e22'
    }
    
    stages {
        stage('build'){
            steps{
                sh 'mvn -s settings.xml -DskipTests install'
            }
            post {
                success {
                    echo "Now Archiving..."
                    archiveArtifacts '**/target/*.war'
                }
            }
        }
        stage('Test') {
            steps{
                sh 'mvn -s settings.xml test'
            }
        }
        stage('Checkstyle Analysis') {
            steps{
                sh 'mvn -s settings.xml Checkstyle:checkstyle'
            }
        }

        stage('code Quality') {
            steps{
                sh 'mvn -s settings.xml sonar:sonar'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                script {
                    scannerHome = tool "${SONARSCANNER}"
                    
                }
                withSonarQubeEnv("${SONARQUBE_SERVER"}) {
                            sh '''${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=vprofile \
                            -Dsonar.projectName=vprofile-repo \
                            -Dsonar.projectVersion=1.0 \
                            -Dsonar.sources=src/ \
                            -Dsonar.sources=src/ \
                            -Dsonar.tests=src/test/java/com/visualpathit/account/\
                            -Dsonar.language=java \
                            -Dsonar.exclusions=**/generated/** \
                            -Dsonar.java.binaries=target/test-classes/com/visualpathit/account/controllerTest/ \
                            -Dsonar.junit.reportsPath=target/surefire-reports/ \
                            -Dsonar.jacoco.reportsPath=target/jacoco.exec \
                            -Dsonar.java.checkstyle.reportPaths=target/checkstyle-result.xml
                            '''
                                    
                    }
            }
        }
    }
}