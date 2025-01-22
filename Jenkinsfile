def registry = 'https://galaxyzz.jfrog.io'

pipeline {

    agent {

        node {

            label 'Linux_Agent'

        }

    }
environment{
    PATH="/opt/apache-maven-3.9.9/bin:$PATH"
}

 

    stages {

        stage('Build') {

            steps {

                sh "mvn clean deploy"

            }

        }
        // stage('SonarQube analysis') {
        //     environment {
        //         scannerHome = tool 'sonarscanner'

        //     }
        //     steps{
        //         withSonarQubeEnv('sonarqube-server') { // If you have configured more than one global server connection, you can specify its name
        //             sh "${scannerHome}/bin/sonar-scanner"
        //         }
        //     }
        // }
             
        stage("Jar Publish") {
            steps {
                script {
                        echo '<--------------- Jar Publish Started --------------->'
                        def server = Artifactory.newServer url:registry+"/artifactory" ,  credentialsId:"JFrog_Artifactory"
                        def properties = "buildid=${env.BUILD_ID},commitid=${GIT_COMMIT}";
                        def uploadSpec = """{
                            "files": [
                                {
                                    "pattern": "jarstaging/*.jar",  // Use a more specific pattern for matching JAR files
                                    "target": "libs-release-local/{1}",  // Target path in Artifactory
                                    "flat": "false",  // Keep the directory structure intact
                                    "props": "${properties}",
                                    "exclusions": ["*.sha1", "*.md5"]  // Exclude checksum files
                                }
                            ]
                        }"""
                    // Upload the artifacts to Artifactory
                    def buildInfo = server.upload(uploadSpec)

                    // Collect environment information and set it in build info
                    buildInfo.env.collect()

                    // Publish the build info to Artifactory
                    server.publishBuildInfo(buildInfo)
                    
                    echo '<--------------- Jar Publish Ended --------------->'    
                
                }
            }   
        }   

    }

}
