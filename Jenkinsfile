pipeline{
    //Directives
    agent any
    tools {
        maven 'Maven'
    }

    stages {
        // Specify various stage with in stages

        // stage 1. Build
        stage ('Build'){
            steps {
                sh 'mvn clean install package'
            }
        }

        // Stage2 : Testing
        stage ('Test'){
            steps {
                echo ' testing......'

            }
        }

        stage ('Publish to Nexus') {
            steps {
                nexusArtifactUploader artifacts: [[artifactId: 'VinayDevOpsLab', 
                classifier: '', 
                file: 'target/VinayDevOpsLab-0.0.9.war', 
                type: 'war']], 
                credentialsId: '8a16801a-e484-4686-a017-f88aba3f8cf6', 
                groupId: 'com.vinaysdevopslab', 
                nexusUrl: '172.20.10.125:8081', 
                nexusVersion: 'nexus3', 
                protocol: 'http', 
                repository: 'VinaysDevOpsLab-RELEASE', 
                version: '0.0.9'
            }
        }

        // Stage3 : Publish the source code to Sonarqube
        stage ('Sonarqube Analysis'){
            steps {
                echo ' Source code published to Sonarqube for SCA......'
                // withSonarQubeEnv('sonarqube'){ // You can override the credential to be used
                //     sh 'mvn sonar:sonar'
                //}

            }
        }

        
        
    }

}
