
def registry = "https://galaxyzz.jfrog.io"
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
        stage('Jar Publish') { 
            steps {
                script {
                    echo '<--------------- Jar Publish Started --------------->'

            // Define the Artifactory server and credentials
            def server = Artifactory.newServer(
                url: registry + "/artifactory", 
                credentialsId: "JFrog_Artifactory"
            )

            // Define properties for the build (build ID and commit ID)
            def properties = "buildid=${env.BUILD_ID},commitid=${GIT_COMMIT}"

            // Define the upload spec, ensuring it's correctly formatted
            def uploadSpec = """{
                "files": [
                    {
                        "pattern": "jarstaging/*.jar",  // Ensure this is the correct path
                        "target": "libs-release-local/${env.BUILD_ID}/",  // Specify the target location in Artifactory
                        "flat": "false",  // Preserve directory structure
                        "props": "${properties}",
                        "exclusions": ["*.sha1", "*.md5"]  // Exclude checksum files
                    }
                ]
            }"""

            try {
                // Upload the artifact to Artifactory using the upload spec
                def buildInfo = server.upload(uploadSpec)

                // Collect environment information and associate it with the build
                buildInfo.env.collect()

                // Publish the build info to Artifactory
                server.publishBuildInfo(buildInfo)

                echo '<--------------- Jar Publish Ended --------------->'
            } catch (Exception e) {
                echo "Error during artifact upload: ${e.getMessage()}"
                currentBuild.result = 'FAILURE'
                throw e  // Re-throw the error to fail the build
            }
        }
   
                
                    
                     
                
            }
        }
    }
}
