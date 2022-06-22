pipeline {
    agent any

    stages {
        stage('Clone git repo') {
            steps {
                sh 'echo "STAGE 0: Cloning app code from SCM ..."'
                git 'https://github.com/mudathirlawal/cicd-with-jenkins-docker-and-aws-eks.git'
            }    
        }        
        stage('Lint all app code') {
            steps {
                sh 'echo "STAGE 1: Checking app code for syntax error ..."'
                
            }
        }   
        stage( 'Build docker image for app' ) {
            steps {
                sh 'echo "STAGE 2: Building and tagging docker image ..."'
                sh 'docker build -t web-app:v1.0 .'
                sh 'docker image ls'                  
            }
        } 
        stage( 'Push image to dockerhub repo' ) {
            steps {
                withDockerRegistry([url: "", credentialsId: "dockerhub"]) {
                    sh 'echo "STAGE 3: Uploading image to dockerhub repository ..."'
                    sh 'docker login'
                    sh 'docker tag web-app:v1.0 anitha7797/web-app:v1.0'
                    sh 'docker push anitha7797/web-app:v1.0'          
                }
            }
        }                                   
        stage( 'Deploy image to AWS EKS' ) {
            steps {
                withAWS( region:'us-east-1', credentials:'k8s' ) {
                    sh 'echo "STAGE 4: Deploying image to AWS EKS cluster ..."'
                    sh 'aws eks update-kubeconfig --name dev --region us-east-1'
                    sh 'kubectl config use-context arn:aws:eks:us-east-1:044804851099:cluster/dev'
                    sh 'kubectl set image deployment web-app web-app=anitha7797/web-app:v1.0'
                    sh 'kubectl rollout status deployment web-app'
                    sh 'kubectl apply -f templates/deployment.yml'
                    sh 'kubectl apply -f templates/loadbalancer.yml'
                    sh 'kubectl apply -f templates/aws-auth-cm.yml'
                    sh 'kubectl get nodes --all-namespaces'
                    sh 'kubectl get deployment'
                    sh 'kubectl get pod -o wide'
                    sh 'kubectl get service/web-app'
                    sh 'echo "Congratulations! Deployment successful."'
                    sh 'kubectl describe deployment/web-app'
                }
            }
        }               
    }
}
