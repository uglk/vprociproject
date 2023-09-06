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
    stages{ 
        stage("Compile"){
            steps{
                sh 'mvn compile -DskipTests'
               
            }
        }
        stage("Unit test"){
            steps{
              sh 'mvn test'
            }
        }
        stage("Check Style Analysis"){
            steps{
                sh 'mvn checkstyle:checkstyle'
            }
        }
        stage("Owasp Scan"){
            steps{
                 dependencyCheck additionalArguments: '--scan ./', odcInstallation: 'dp-check'
                    dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
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