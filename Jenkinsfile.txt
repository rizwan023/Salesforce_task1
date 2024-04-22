pipeline {
    agent any
    stages {
        stage('Clone Repository') {
            steps {
                git(url: 'https://github.com/surya123789/task.git')
            }
        }
        stage('Build Docker Image') {
            when {
                expression { currentBuild.resultIsBetterOrEqualTo('SUCCESS') }
            }
            steps {
                script {
                    sh '''
                    docker build -t 586192913683.dkr.ecr.eu-west-1.amazonaws.com/elpdevops:$BUILD_NUMBER .
                    '''
                }
            }
        }
        stage('Push Docker Image') {
            when {
                expression { currentBuild.resultIsBetterOrEqualTo('SUCCESS') }
            }
            steps {
                script {
                    sh '''
                    aws ecr get-login-password --region eu-west-1 | docker login --username AWS --password-stdin 586192913683.dkr.ecr.eu-west-1.amazonaws.com
                    docker push 586192913683.dkr.ecr.eu-west-1.amazonaws.com/elpdevops:$BUILD_NUMBER
                    '''
                }
            }
        }
    }
}
