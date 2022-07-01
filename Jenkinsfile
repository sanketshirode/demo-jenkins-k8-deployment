pipeline {
    agent {
        kubernetes {
            defaultContainer 'jnlp'
            yamlFile 'agentpod.yaml'
        }
    }   
    options {
        skipStagesAfterUnstable()
    }
    stages {
         stage('Clone repository') { 
            steps { 
                script{
                checkout scm
                }
            }
        }
        
        stage('Build') { 
            steps { 
                container('docker') {
                    script{
                    app = docker.build("nginx")
                    }
                }
            }
        }
        stage('Test'){
            steps {
                 echo 'Empty'
            }
        }
        stage('Push') {
            steps {
                container('docker') {
                    script{
                        docker.withRegistry('https://public.ecr.aws/l6v9t3l4/demo-jenkins-k8-deployment', 'ecr:us-east-1:aws-credentials') {
                        app.push("${env.BUILD_NUMBER}")
                        app.push("latest")
                        }
                    }
                }
            }
        }
        stage('Deploy'){
            steps {
                 sh 'kubectl apply -f deployment.yml'
                //  sh 'kubectl rollout restart deployment nginx'
            }
        }
        
    }
}
