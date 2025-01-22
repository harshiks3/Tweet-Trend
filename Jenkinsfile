
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
                    def server = Artifactory.server server: 'https://galaxyzz.jfrog.io', credentialsId: 'JFrog_Artifactory' 
                    server.upload spec: """
                        {
                            "files": [
                                {
                                    "pattern": "target/*.jar", 
                                    "target": "libs-release-local", 
                                    "flat": true,
                                    "props": "build.number=${BUILD_NUMBER}"
                                }
                            ]
                        }
                    """
                            
                    echo '<--------------- Jar Publish Ended --------------->'
                } 
            }
        }
    }
}
