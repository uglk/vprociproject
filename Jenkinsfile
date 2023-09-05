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
    }
    stages{
        stage("Compile"){
            steps{
                sh 'mvn clean install package -DskipTests'
            }
            post{
                success{
                    echo "Now Archieving"
                    archiveArtifacts artifacts: '**/*.war'
                }
            }
        }
    }
}