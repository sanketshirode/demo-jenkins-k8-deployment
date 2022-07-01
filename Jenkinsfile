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
                    app = docker.build("demo-nginx")
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
                        docker.withRegistry('https://438748127802.dkr.ecr.us-west-2.amazonaws.com/demo-nginx', 'ecr:us-west-2:aws-credentials') {
                        app.push("${env.BUILD_NUMBER}")
                        app.push("latest")
                        }
                    }
                }
            }
        }
        stage('Deploy'){

        steps {
                withKubeConfig([credentialsId: 'demokubeconfig', serverUrl: 'https://2EB30681541BE823D9A959E85C8382EB.gr7.us-west-2.eks.amazonaws.com']) {
                sh 'kubectl apply -f deployment.yml'
                }
                // kubernetesDeploy(configs: "deployment.yml", kubeconfigId: "")
                // sh 'kubectl apply -f deployment.yml'
                //  sh 'kubectl rollout restart deployment nginx'
            }
        }
        
    }
}
