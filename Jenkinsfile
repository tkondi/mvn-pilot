pipeline{
    //Directives
    agent any
    tools {
        maven 'maven'
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

        // Stage 3 publish the artifacts to Nexus
               stage ('Publish to Nexus'){
            steps {
                nexusArtifactUploader artifacts: [[artifactId: 'LVSDevOps', classifier: '', file: 'target/LVSDevOps-0.0.9.war', type: 'war']], credentialsId: '3e17c3f7-d5b9-41a4-8a1c-c790a6957235', groupId: 'com.lvsdevops', nexusUrl: '172.20.10.223:8081', nexusVersion: 'nexus3', protocol: 'http', repository: 'LVS-DevOps-SNAPSHOT', version: '0.0.9'
            }
        }

 
        // Stage3 : Publish the source code to Sonarqube
        /*stage ('Sonarqube Analysis'){
            steps {
                echo ' Source code published to Sonarqube for SCA......'
                withSonarQubeEnv('sonarqube'){ // You can override the credential to be used
                     sh 'mvn sonar:sonar'
                }

            }
        }
        */
        
        
    }

}