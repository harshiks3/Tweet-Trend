def registry = 'https://galaxyzz.jfrog.io'

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
    stage("Jar Publish") {
      steps {
        script {
          echo '<--------------- Jar Publish Started --------------->'

          // Define the server and credentials using Artifactory plugin
          def server = Artifactory.server url: registry + "/artifactory", credentialsId: "JFrog_Artifactory"

          // Define build properties (like build ID and commit ID)
          def properties = "buildid=${env.BUILD_ID},commitid=${GIT_COMMIT}"

          // Define the upload specification
          def uploadSpec = """
          {
            "files": [
              {
                "pattern": "jarstaging/*.jar",
                "target": "libs-release-local/${env.BUILD_ID}/",
                "flat": false,
                "props": "${properties}",
                "exclusions": ["*.sha1", "*.md5"]
              }
            ]
          }"""

          // Upload the artifact to Artifactory using Artifactory plugin method
          try {
            server.upload spec: uploadSpec

            // Collect environment info and associate it with the build (Optional)
            // server.publishBuildInfo(buildInfo)

            echo '<--------------- Jar Publish Ended --------------->'
          } catch (Exception e) {
            echo "Error during artifact upload: ${e.getMessage()}"
            throw e
          }
        }
      }
    }
  }
}