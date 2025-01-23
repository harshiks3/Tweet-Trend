pipeline {
    agent any

    environment {
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
                    def properties = "buildid=54,commitid=9d01e0adc5a1f19635c71fb461896aac54e10c63"

                    // Define the upload spec for Artifactory
                    def uploadSpec = """
                    {
                        "files": [
                            {
                                "pattern": "target/*.jar",  // Path to the files you want to upload
                                "target": "libs-release-local/54/",  // Target repository in Artifactory
                                "flat": "false",  // Keep the directory structure intact
                                "props": "buildid=54,commitid=9d01e0adc5a1f19635c71fb461896aac54e10c63",  // Attach properties to the uploaded files
                                "exclusions": ["*.sha1", "*.md5"]  // Exclude checksum files
                            }
                        ]
                    
                    }
                    """

                    // Store the uploadSpec in a global variable to be used in the next stage
                    currentBuild.description = uploadSpec
                    echo "Upload Spec: ${uploadSpec}"
                }
            }
        }

        stage('Upload to Artifactory') {
            steps {
                script {
                    // Retrieve the uploadSpec from the previous stage (global context)
                    def uploadSpec = currentBuild.description

                    if (!uploadSpec) {
                        error "Upload Spec not found. Cannot proceed with Artifactory upload."
                    }

                    // Initialize Artifactory server connection again for the upload step
                    def server = Artifactory.newServer(
                        url: ARTIFACTORY_URL, 
                        credentialsId: ARTIFACTORY_CREDENTIALS
                    )

                    // Upload the artifact(s) using the uploadSpec defined in the previous stage
                    def buildInfo = server.upload(uploadSpec)

                    // Output the build info to verify the upload was successful
                    echo "Build Info: ${buildInfo}"

                    // Publish the build info to Artifactory for tracking
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
