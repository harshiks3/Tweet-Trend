pipeline {
    agent {
        node {
            label 'Linux_Agent'
        }
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