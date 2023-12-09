@Library('jenkins-shared-library') _

pipeline{

    agent any

    parameters{
        choice(name: 'action', choices: 'create\ndelete', description: 'Choose Create/Destroy')
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
    }
    
}
