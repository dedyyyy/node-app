pipeline {
    agent any
    stages{
        stage('Build Docker Image'){
            steps{
                script{
                    def tag = latestCommitHash()
                    sh "docker build . -t dedyyyy/nodeapp:${tag} "
                }
                
            }

        }
        stage('Push DockerHub'){
            steps{
                withCredentials([string(credentialsId: 'docker-hub', variable: 'dockerHubPwd')]) {
                    sh "docker login -u dedyyyy -p ${dockerHubPwd}"
                    script{
                        def tag = latestCommitHash()
                        sh "docker push dedyyyy/nodeapp:${tag}"
                    }
                }
            }
        }
        stage('Deploy - Kubernetes'){
            steps{
                sshagent(['kops-k8s']) {
                    sh """ 
                       scp -o StrictHostKeyChecking=no services.yml pods.yml ec2-user@52.66.70.61:/home/ec2-user/
                       ssh ec2-user@52.66.70.61 kubectl create -f pods.yml
                       ssh ec2-user@52.66.70.61 kubectl create -f services.yml
                    """
                }
            }
        }
    }
}

def latestCommitHash(){
    def commit =  sh returnStdout: true, script: 'git rev-parse HEAD'
    return commit
}