pipeline {
    agent {
        node {
            label 'AGENT-1'
        }
    }
    options {
        ansiColor('xterm')
        // timeout(time: 1, unit: 'HOURS')
        // disableConcurrentBuilds()
    }
    parameters {
        choice(name: 'action', choices: ['apply', 'destroy'], description: 'Pick something')
    }
    // build
    stages {
        stage('Init') {
            steps {
                sh """
                    cd 01-vpc
                    terraform init -reconfigure
                """
            }
        }
        stage('VPC'){
            steps {
                sh """
                    cd 01-vpc
                    terraform ${params.action} -auto-approve
                """
            }
        }
        stage('SG'){
            steps {
                sh """
                    cd 02-sg
                    terraform ${params.action} -auto-approve
                """
            }
        }
        stage('VPN'){
            steps {
                sh """
                    cd 03-vpn
                    terraform ${params.action} -auto-approve
                """
            }
        }
        stage('DB ALB') {
            parallel {
                stage('DB') {
                    steps {
                        sh """
                            cd 04-databases
                            terraform ${params.action} -auto-approve
                        """
                    }
                }
                stage('ALB') {
                    steps {
                        sh """
                            cd 05-app-alb
                            terraform ${params.action} -auto-approve
                        """
                    }
                }
            }
        }
        
    }
    // post build
    post { 
        always { 
            echo 'I will always say Hello again!'
            deleteDir()
        }
        failure { 
            echo 'this runs when pipeline is failed, used generally to send some alerts'
        }
        success{
            echo 'I will say Hello when pipeline is success'
        }
    }
}