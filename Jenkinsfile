pipeline{
    //Directives
    agent any
    tools {
        maven 'maven'
    }
    environment{
        ArtifactId = readMavenPom().getArtifactId()
        Version = readMavenPom().getVersion()
        Name = readMavenPom().getName()
        GroupId = readMavenPom().getGroupId()
    }

    stages {
        // Specify various stage with in stages

        // stage 1. Build
        stage ('Build'){
            steps {
                sh 'mvn clean install package'
            }
        }

        // Stage2 : Publish the source code to Sonarqube
        stage ('Sonarqube Analysis'){
            steps {
                echo ' Source code published to Sonarqube for SCA......'
                withSonarQubeEnv('sonarqube'){ // You can override the credential to be used
                     sh 'mvn sonar:sonar'
                }

            }
        }

        // Stage 3 publish the artifacts to Nexus
        stage ('Publish to Nexus'){
            steps {
                script {

                    def NexusRepo = Version.endsWith("SNAPSHOT") ? "LVS-DevOps-SNAPSHOT" : "LVS-DevOps-RELEASE"

                    nexusArtifactUploader artifacts: 
                    [[artifactId: "${ArtifactId}", 
                    classifier: '', 
                    file: "target/${ArtifactId}-${Version}.war", 
                    type: 'war']], 
                    credentialsId: '3e17c3f7-d5b9-41a4-8a1c-c790a6957235', 
                    groupId: "${GroupId}", 
                    nexusUrl: '172.20.10.223:8081', 
                    nexusVersion: 'nexus3', 
                    protocol: 'http', 
                    repository: "${NexusRepo}", 
                    version: "${Version}"
                }
            }
        }
        /*
         // Stage4 : Print some informations
        stage ('Print informations'){
            steps {
                echo "Artifact ID is '${ArtifactId}'"
                echo "Version is '${Version}'"
                echo "GroupID is is '${GroupId}'"
                echo "Name is '${Name}'"
            }
        }
        */
        // Stage5 : Deploy
        stage ('Deploy to Tomcat'){
            steps {
                echo "Deploying..."
                sshPublisher(publishers: 
                [sshPublisherDesc(
                    configName: 'Ansible-Controller', 
                    transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'ansible-playbook /opt/playbooks/downloadanddeploy.yaml -i /opt/playbooks/hosts', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '')], 
                    usePromotionTimestamp: false, 
                    useWorkspaceInPromotion: false, 
                    verbose: false)])
            }
        }

        // Stage6 : Deploying to docker
        stage ('Deploy to Docker'){
            steps {
                echo "Deploying..."
                sshPublisher(publishers: 
                [sshPublisherDesc(
                    configName: 'Ansible-Controller', 
                    transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'ansible-playbook /opt/playbooks/downloadanddeploy_docker.yaml -i /opt/playbooks/hosts', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '')], 
                    usePromotionTimestamp: false, 
                    useWorkspaceInPromotion: false, 
                    verbose: false)])
            }
        }
        
        
    }

}