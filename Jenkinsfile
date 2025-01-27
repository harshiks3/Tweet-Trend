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
                sh 'mvn deploy'
            }
        }
        stage('send artifact'){
           steps {
                script {
                    def server = Artifactory.server("https://galaxyzz.jfrog.io")
                    def uploadSpec = """
                    {
                        "files": [
                            {
                                "pattern": "**/*.jar",
                                "target": "${REPO_NAME}/"
                            }
                        ]
                    }
                    """

                    // Upload artifacts to Artifactory
                    server.upload(spec: uploadSpec)
                }
            }
            
            
        }        
    }
}