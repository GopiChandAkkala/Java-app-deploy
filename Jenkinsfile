@Library('jenkins-shared-library') _

pipeline{

    agent any

    parameters{
        choice(name: 'action', choices: 'create\ndelete', description: 'Choose Create/Destroy')
        string(name: 'DockerHubUser', description: 'Enter DockerHub User', defaultValue: 'akkalagopi')
        string(name: 'ProjectName', description: 'Enter the Project Name', defaultValue: 'java-app')
        string(name: 'ImageTag', description: 'Enter Tag for Image', defaultValue: 'v1')
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

        stage("Trivy Image Scan"){
            when { expression { params.action == 'create'}}
            steps{
                script{
                    trivyImageScan("${params.DockerHubUser}","${params.ProjectName}","${params.ImageTag}")
                }
            }
        }
    }
    
}
