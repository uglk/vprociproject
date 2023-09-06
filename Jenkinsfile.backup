def COLOR_MAP = [
    'SUCCESS': 'good', 
    'FAILURE': 'danger',
]
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
                sh 'time mvn package -DskipTests'
               
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
                sh 'time mvn test'
              
            }

        }
        stage("Check Style Analysis"){
            steps{
                sh 'time mvn checkstyle:checkstyle'
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
        stage("UploadArtifact"){
            steps{
                nexusArtifactUploader(
                  nexusVersion: 'nexus3',
                  protocol: 'http',
                  nexusUrl: "${NEXUSIP}:${NEXUSPORT}",
                  groupId: 'QA',
                  version: "${env.BUILD_ID}-${env.BUILD_TIMESTAMP}",
                  repository: "${RELEASE_REPO}",
                  credentialsId: "${NEXUS_LOGIN}",
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
    post {
        always {
            echo 'Slack Notifications.'
            slackSend channel: '#jenkinscicd',
                color: COLOR_MAP[currentBuild.currentResult],
                message: "*${currentBuild.currentResult}:* Job ${env.JOB_NAME} build ${env.BUILD_NUMBER} \n More info at: ${env.BUILD_URL}"
        }
    }
}