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
                    // Ensure that the Artifactory server object is initialized correctly
                    def server = null

                    try {
                        // Define the Artifactory server with the correct ID
                        server = Artifactory.server('myArtifactoryServer')  // Correct initialization
                        server.url = 'https://galaxyzz.jfrog.io'  // Set the Artifactory server URL
                        server.credentialsId = 'JFrog_Artifactory'  // Set the credentials ID for Artifactory

                        stage('Build') {
                            // Your build steps
                            echo 'Building the project...'
                        }

                        stage('Upload to Artifactory') {
                            // Define the upload spec (upload artifact to Artifactory)
                            def uploadSpec = '''{
                                "files": [
                                    {
                                        "pattern": "target/*.jar",
                                        "target": "libs-release-local/54/"
                                    }
                                ]
                            }'''

                            // Check if server is properly initialized before attempting upload
                            if (server) {
                                echo 'Uploading artifacts to Artifactory...'
                                server.upload(uploadSpec)  // Upload the artifacts to Artifactory
                            } else {
                                error "Artifactory server is not initialized!"
                            }
                        }
                        
                    } catch (Exception e) {
                        currentBuild.result = 'FAILURE'
                        throw e
                    }
                }

            }
        }
    }
}