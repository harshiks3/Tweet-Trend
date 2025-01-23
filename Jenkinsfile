pipeline {
    agent {
        node {
            label 'Linux_Agent'
        }
    }
    stages {
        stage('Preparation') {
            echo "Preparing for artifact upload"
            
            // Define the Artifactory server URL and credentials
            def server = Artifactory.newServer(
                url: "https://https://galaxyzz.jfrog.io/artifactory", 
                credentialsId: "JFrog_Artifactory"
            )
            
            // Define properties for the build (build ID, commit ID, etc.)
            def properties = "buildid=${env.BUILD_ID},commitid=${GIT_COMMIT}"
            
            // Define the upload specification (uploadSpec) for Artifactory
            def uploadSpec = """
            {
                "files": [
                    {
                        "pattern": "target/*.jar",  // Path to the files you want to upload
                        "target": "libs-release-local/${env.BUILD_ID}/",  // Target repository and directory in Artifactory
                        "flat": "false",  // Keep the directory structure intact
                        "props": "${properties}",  // Attach properties to the uploaded files
                        "exclusions": ["*.sha1", "*.md5"]  // Exclude checksum files
                    }
                ]
            }
            """

            // Output the uploadSpec to verify
            echo "Upload Spec: ${uploadSpec}"
        }

        stage('Upload to Artifactory') {
            echo "Uploading artifacts to Artifactory"
            
            // Upload the artifact(s) to Artifactory using the specified uploadSpec
            def buildInfo = server.upload(uploadSpec)

            // Log the build information to verify the upload
            echo "Build Info: ${buildInfo}"

            // Collect environment information (e.g., build ID, Git commit ID, etc.)
            buildInfo.env.collect()

            // Publish the build information to Artifactory
            server.publishBuildInfo(buildInfo)
        }

        stage('Success') {
            echo "Artifact upload completed successfully"
        }
            
        
    }
}
