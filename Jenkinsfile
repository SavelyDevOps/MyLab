pipeline{
    //Directives
    agent any
    tools {
        maven 'Maven'
    }

    environment {
        GroupId = readMavenPom().getGroupId()
        ArtifactId = readMavenPom().getArtifactId()
        Version = readMavenPom().getVersion()
        Name = readMavenPom().getName()
        Packaging = readMavenPom().getPackaging()
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

        stage ('Print Environment') {
            steps {
                echo "Artifact ID is '${ArtifactId}'"
                echo "Group ID is '${GroupId}'"
                echo "Version is '${Version}'"
                echo "Name is '${Name}'"
                echo "Packaging is '${Packaging}'"
            }
        }

        stage ('Publish to Nexus') {
            steps {
                script {
                    def NexusRepo = Version.endsWith("SNAPSHOT") ? "${Name}-SNAPSHOT" : "${Name}-RELEASE"

                    nexusArtifactUploader artifacts: [[artifactId: "${ArtifactId}", 
                    classifier: '', 
                    file: "target/${ArtifactId}-${Version}.${Packaging}", 
                    type: 'war']], 
                    credentialsId: '8a16801a-e484-4686-a017-f88aba3f8cf6', 
                    groupId: "${GroupId}", 
                    nexusUrl: '172.20.10.125:8081', 
                    nexusVersion: 'nexus3', 
                    protocol: 'http', 
                    repository: "${NexusRepo}", 
                    version: "${Version}"                       
                }
           }
        }

        stage('Deploy') {
            steps {
                echo ' Deploying to Tomcat......'
                sshPublisher(publishers: [sshPublisherDesc(
                    configName: 'Ansible_Controller', 
                    transfers: [
                        sshTransfer(
                            cleanRemote: false, 
                            execCommand: 'ansible-playbook /opt/playbooks/downloadanddeploy_as_tomcat_user.yaml -i /opt/playbooks/hosts', 
                            execTimeout: 120000
                            )
                        ], 
                    usePromotionTimestamp: false, 
                    useWorkspaceInPromotion: false, 
                    verbose: false)])
            }
        }

        
        stage('Deploy to Docker') {
            steps {
                echo ' Deploying to Docker......'
                sshPublisher(publishers: [sshPublisherDesc(
                    configName: 'Ansible_Controller', 
                    transfers: [
                        sshTransfer(
                            cleanRemote: false, 
                            execCommand: 'ansible-playbook /opt/playbooks/downloadanddeploy_docker.yaml -i /opt/playbooks/hosts', 
                            execTimeout: 120000
                            )
                        ], 
                    usePromotionTimestamp: false, 
                    useWorkspaceInPromotion: false, 
                    verbose: false)])
            }
        }

        // Stage3 : Publish the source code to Sonarqube
        stage ('Sonarqube Analysis'){
            steps {
                echo ' Source code published to Sonarqube for SCA......'
                withSonarQubeEnv('sonarqube'){ // You can override the credential to be used
                     sh 'mvn sonar:sonar'
                }
            }
        }       
        
    }

}
