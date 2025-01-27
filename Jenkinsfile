pipeline {
    agent {
        node {
            label 'Linux_Agent'
        }
    }
    environment {
        PATH = "/opt/apache-maven-3.9.9/bin:$PATH"
        registry = "https://galaxyzz.jfrog.io" // Artifactory registry URL
        credentialsId = "JFrog_Artifactory"   // Artifactory credentials ID
    }

    stages {
        stage('Build') {
            steps {
                sh 'mvn deploy'
            }
        }
        // stage('send artifact'){
        //    steps {
        //         script {
        //             def server = Artifactory.server("https://galaxyzz.jfrog.io")
        //             def uploadSpec = """
        //             {
        //                 "files": [
        //                     {
        //                         "pattern": "**/*.jar",
        //                         "target": "${REPO_NAME}/"
        //                     }
        //                 ]
        //             }
        //             """

        //             // Upload artifacts to Artifactory
        //             server.upload(spec: uploadSpec)
        //         }
        //     }
            
            
        // }    
    
        stage("Jar Publish") {
            steps {
                script {
                    echo '<--------------- Jar Publish Started --------------->'

                    // Define the Artifactory server
                    def server = Artifactory.newServer(
                        url: "${registry}/artifactory",
                        credentialsId: credentialsId
                    )

                    // Set build properties
                    def properties = "buildid=${env.BUILD_ID},commitid=${env.GIT_COMMIT ?: 'unknown'}"

                    // Upload specification
                    def uploadSpec = """{
                        "files": [
                            {
                                "pattern": "jarstaging/*",
                                "target": "libs-release-local/",
                                "props": "${properties}",
                                "flat": true
                            }
                        ]
                    }"""

                    try {
                        // Upload artifacts to Artifactory
                        def buildInfo = server.upload(uploadSpec)

                        // Collect build environment variables
                        buildInfo.env.collect()

                        // Publish build info to Artifactory
                        server.publishBuildInfo(buildInfo)

                        echo '<--------------- Jar Publish Ended Successfully --------------->'
                    } catch (Exception e) {
                        error "Error occurred during JAR publish: ${e.message}"
                    }
                }
            }
        }
       
    }
}