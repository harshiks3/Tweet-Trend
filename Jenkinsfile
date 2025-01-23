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
                    def server = Artifactory.server server: 'https://galaxyzz.jfrog.io', credentialsId: 'JFrog_Artifactory'
                    server.upload spec: """
                        {
                            "files": [
                                {
                                    "pattern": "target/*.jar", 
                                    "target": "libs-release-local", 
                                    "flat": true,
                                    "props": [ 
                                        "build.number=${BUILD_NUMBER}" 
                                    ] 
                                }
                            ]
                        }
                    """
                }
            }
        }
    }
}