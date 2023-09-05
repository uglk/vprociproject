pipeline{
    agent any
    tools{
     maven "MAVEN3"
     jdk "jdk8"
    }
    environment{
        SNAP_REPO= 'vprofile-snapshot'
        NEXUS_USER= 'admin'
        NEXUS_PASS= 'admin'
        RELEASE_REPO= 'vprofile-release'
        CENTRAL_REPO= 'vpro-maven-central'
        NEXUSIP= '10.0.28.71'
        NEXUSPORT= '8081'
        NEXUS_GRP_REPO= 'vpro-maven-group'
        NEXUS_LOGIN= 'nexuslogin'
        SONARSERVER = 'sonarserver'
        SONARSCANNER = 'sonarscanner'
    }
    stages{
        stage("Compile"){
            steps{
                sh 'mvn package -DskipTests'
                sh 'time'
            }
            post{
                success{
                    echo "Now Archieving"
                    archiveArtifacts artifacts: '**/*.war'
                }
            }
        }
        stage("Unit Test"){
            steps{
                sh 'mvn test '
                sh 'time'
            }

        }
        stage("Check Style Analysis"){
            steps{
                sh 'mvn checkstyle:checkstyle'
            }

        }
        stage("Sonar Analysis"){
            environment{
                scannerHome = tool "$SONARSCANNER"
            }
            steps{
                withSonarQubeEnv("${SONARSERVER}") {
                 sh '''${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=vprofile \
                    -Dsonar.projectName=vprofile-repo \
                    -Dsonar.projectVersion=1.0 \
                    -Dsonar.sources=src/ \
                    -Dsonar.java.binaries=target/test-classes/com/visualpathit/account/controllerTest/ \
                    -Dsonar.junit.reportsPath=target/surefire-reports/ \
                    -Dsonar.jacoco.reportsPath=target/jacoco.exec \
                    -Dsonar.java.checkstyle.reportPaths=target/checkstyle-result.xml'''
            }
            }
        }
        stage("Sonar Quality"){
            steps{
                timeout(time: 1, unit: 'HOURS'){
                 waitForQualityGate abortPipeline: true
                }
            }
        }
        // stage("Nexus Artifact Uploader"){

        // }
    }
}