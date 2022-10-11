pipeline {
    agent any
    options {
        skipDefaultCheckout(true)
    }

    stages {
        stage('Get code from SCM'){
            steps{
                git branch: 'master', credentialsId: 'githubpass', url: 'https://github.com/Josetemitayo96/verifymetest.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t 198309634927.dkr.ecr.us-east-1.amazonaws.com/verifyapp .'
            }
        }

        stage('Login And Push Image') {
            steps {
                withAWS(credentials: 'aws-cred', region: 'us-east-1') {
                    sh 'aws ecr get-login-password --region us-east-2 | docker login --username AWS --password-stdin 107327911397.dkr.ecr.us-east-2.amazonaws.com'
                    sh 'docker push 198309634927.dkr.ecr.us-east-1.amazonaws.com/verifyapp'
                    sh 'docker rmi 198309634927.dkr.ecr.us-east-1.amazonaws.com/verifyapp'
                }
            }
        }

        stage('Update the fargate service') {
            steps {
                withAWS(credentials: 'aws-cred', region: 'us-east-1') {
                    sh 'aws ecs update-service --cluster  verifyme --force-new-deployment --service verifyapp'
                }
            }
        }

    }
    post {
        always {
            deleteDir()
        }
    }
}


