pipeline {
    agent any

    environment {
        // Define any environment variables you need
        ARTIFACTORY_URL = "https://galaxyzz.jfrog.io/artifactory"
        ARTIFACTORY_CREDENTIALS = "JFrog_Artifactory"
    }

    stages {
        stage('Preparation') {
            steps {
                script {
                    // Initialize Artifactory server connection
                    def server = Artifactory.newServer(
                        url: ARTIFACTORY_URL, 
                        credentialsId: ARTIFACTORY_CREDENTIALS
                    )

                    // Define build properties
                    def properties = "buildid=${env.BUILD_ID},commitid=${GIT_COMMIT}"

                    // Define the upload spec for Artifactory
                    def uploadSpec = """
                    {
                        "files": [
                            {
                                "pattern": "target/*.jar",  // Path to the files you want to upload
                                "target": "libs-release-local/${env.BUILD_ID}/",  // Target repository in Artifactory
                                "flat": "false",  // Keep the directory structure intact
                                "props": "${properties}",  // Attach properties to the uploaded files
                                "exclusions": ["*.sha1", "*.md5"]  // Exclude checksum files
                            }
                        ]
                    }
                    """
                    // Output the upload spec to verify
                    echo "Upload Spec: ${uploadSpec}"
                }
            }
        }

        stage('Upload to Artifactory') {
            steps {
                script {
                    // Upload the artifacts using the defined upload spec
                    def server = Artifactory.newServer(
                        url: ARTIFACTORY_URL, 
                        credentialsId: ARTIFACTORY_CREDENTIALS
                    )

                    // Upload the artifact(s)
                    def buildInfo = server.upload(uploadSpec)

                    // Output the build info to verify
                    echo "Build Info: ${buildInfo}"

                    // Publish the build info to Artifactory
                    server.publishBuildInfo(buildInfo)
                }
            }
        }

        stage('Success') {
            steps {
                echo 'Artifact upload completed successfully!'
            }
        }
    }

    post {
        failure {
            echo 'The pipeline has failed. Check the logs for details.'
        }
    }
}
