@Library('jenkins-shared-library') _

pipeline{

    agent any

    parameters{
        choice(name: 'action', choices: 'create\ndelete', description: 'Choose Create/Destroy')
        string(name: 'DockerHubUser', description: 'Enter DockerHub User', defaultValue: 'akkalagopi')
        string(name: 'ProjectName', description: 'Enter the Project Name', defaultValue: 'java-app')
        string(name: 'ImageTag', description: 'Enter Tag for Image', defaultValue: 'v1')
    }
    
    environment {        
        AWS_ACCESS_KEY_ID = credentials('access-key-id')
        AWS_SECRET_ACCESS_KEY = credentials('aws-secret-access-key')
    }

    stages{
        stage("Git Checkout"){
            when { expression { params.action == 'create'}}
            steps{
                
                script{
                    gitCheckout(
                        branch: "main",
                        url: "https://github.com/GopiChandAkkala/Java-app-deploy.git"
                    )
                }
            }
            
        }
        /*
        stage("Unit Test"){
            when { expression { params.action == 'create'}}
            steps{
                script{
                    mvnTest()
                }
            }
        }

        stage("Integration Test Maven"){
            when { expression { params.action == 'create'}}
            steps{
                script{
                    mvnIntegration()
                }
            }
        }

        stage("Static Code Analysis: Sonarqube"){
            when { expression { params.action == 'create'}}
            steps{
                script{
                    def sonarqubecredentialsId = 'sonarqube-api'
                    staticCodeAnalysis(sonarqubecredentialsId)
                }
            }
        }

        stage("Quality Gate Status: Sonarqube"){
            when { expression { params.action == 'create'}}
            steps{
                script{
                    def sonarqubecredentialsId = 'sonarqube-api'
                    qualityGateStatus(sonarqubecredentialsId)
                }
            }
        }
        */
        stage("Maven Build"){
            when { expression { params.action == 'create'}}
            steps{
                script{
                    mvnBuild()
                }
            }
        }

        stage("Docker Build Image"){
            when { expression { params.action == 'create'}}
            steps{
                script{
                    dockerBuild("${params.DockerHubUser}","${params.ProjectName}","${params.ImageTag}")
                }
            }
        }
        /*
        stage("Trivy Image Scan"){
            when { expression { params.action == 'create'}}
            steps{
                script{
                    trivyImageScan("${params.DockerHubUser}","${params.ProjectName}","${params.ImageTag}")
                }
            }
        }
        */
        stage("Docker Image Push"){
            when { expression { params.action == 'create'}}
            steps{
                script{
                    dockerHubImagePush("${params.DockerHubUser}","${params.ProjectName}","${params.ImageTag}")
                }
            }
        }

        /*stage("Docker Image Clean"){
            when { expression { params.action == 'create'}}
            steps{
                script{
                    dockerImageClean("${params.DockerHubUser}","${params.ProjectName}","${params.ImageTag}")
                }
            }
        }*/

        stage('Terraform Init and Apply') {
            steps {
                dir('terraform/') {
                    script {
                        sh 'terraform init -no-color'
                        def tfOutput = sh(script: 'terraform apply -auto-approve', returnStdout: true).trim()
                    }
                }
            }
        }

        stage('Terraform get IP') {
            steps {
                dir('terraform/') {
                    script {
                        ec2PublicIp = sh(script: 'terraform output -json ec2_instance_public_ip', returnStdout: true).trim()

                        withEnv(['EC2_PUBLIC_IP=' + ec2PublicIp]) {
                            echo "EC2 Public IP: ${EC2_PUBLIC_IP}"
                        }
                    }
                }
            }
        }

        stage('ansible playbook get IP') {
            steps {
                dir('ansible/') {
                    script {
                        echo "About to create inventory file"
                        writeFile file: 'inventory.ini', text: "my-ec2 ansible_host=${ec2PublicIp} ansible_user=ec2-user"
                        echo "Inventory file is created"
                    }
                }
            }
        }

        stage('ansible play playbook') {
            steps {
                dir('ansible/') {
                    script {
                        withCredentials([sshUserPrivateKey(credentialsId: 'aws-keypair', keyFileVariable: 'SSH_PRIVATE_KEY')]) {
                            sh """
                                ansible-playbook -i inventory.ini  main.yml --private-key=\$SSH_PRIVATE_KEY --become
                            """
                        }
                    }
                }
            }
        }
    }
    
}
