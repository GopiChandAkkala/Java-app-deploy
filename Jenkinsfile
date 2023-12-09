@Library('jenkins-shared-library') _

pipeline{

    agent any

    stages{
        stage("Git Checkout"){
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
            steps{
                script{
                    mvnTest()
                }
            }
        }

        stage("Unit Test Integration"){
            steps{
                script{
                    mvnIntegration()
                }
            }
        }
    }
    
}
