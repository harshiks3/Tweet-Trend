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
            def uploadSpec = '''{
                "files": [
                    {
                        "pattern": "target/*.jar",
                        "target": "libs-release-local/"
                    }
                ]
            }'''
            server.upload(uploadSpec)
            
            
        }        
    }
}