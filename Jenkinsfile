pipeline {
    agent {
        node {
            label 'Linux_Agent'
        }
    }
    environment {
        PATH = "/opt/apache-maven-3.9.9/bin:$PATH"
    }

    stages {
        stage('Build') {
            steps {
                sh 'mvn clean install'
            }
        }
        stage('Publish to Artifactory') {
            steps {
                script {
                    def server = Artifactory.server(
                        [
                    
                            url: 'https://galaxyzz.jfrog.io',
                            credentialsId: 'JFrog_Artifactory'
                        ]
                    )

                    // OR, if you have a pre-configured server in Jenkins:
                    // def server = Artifactory.server('myArtifactoryServerName')

                    def rtUpload = Artifactory.newUploadSpec()
                    rtUpload.set("files[*].{pattern: 'target/*.jar', target: 'libs-release-local/'}") // Example path

                    server.upload(rtUpload)
                }
            }
                            
                       
                
            
        }
    }
}