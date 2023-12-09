pipeline{

    agent any

    stages{
        stage("Git Checkout"){
            steps{
                
                script{
                    git branch: 'main', credentialsId: 'github', url: 'https://github.com/GopiChandAkkala/Java-app-deploy.git'
                }
            }
            
        }
    }
    
}