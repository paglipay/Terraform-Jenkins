pipeline {

    parameters {
        booleanParam(name: 'autoApprove', defaultValue: false, description: 'Automatically run apply after generating plan?')
    } 
    environment {
        // AWS_ACCESS_KEY_ID     = credentials('AWS_ACCESS_KEY_ID')
        // AWS_SECRET_ACCESS_KEY = credentials('AWS_SECRET_ACCESS_KEY')
        AWS_ACCESS_KEY_ID     = 'AKIASWK2AEZTDFHWZW6F'
        AWS_SECRET_ACCESS_KEY = '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'
    }

    agent {
        label 'ub-11'
    }
    stages {
        stage('Checkout') {
            steps {
                 script{
                        dir("terraform")
                        {
                            // git "https://github.com/paglipay/Terraform-Jenkins.git"
                            git branch: 'main', url: 'https://github.com/paglipay/Terraform-Jenkins.git'
                        }
                    }
                }
            }

        stage('Plan') {
            steps {
                sh 'pwd;cd terraform/ ; terraform init'
                sh "pwd;cd terraform/ ; terraform plan -out tfplan"
                sh 'pwd;cd terraform/ ; terraform show -no-color tfplan > tfplan.txt'
            }
        }
        stage('Approval') {
           when {
               not {
                   equals expected: true, actual: params.autoApprove
               }
           }

           steps {
               script {
                    def plan = readFile 'terraform/tfplan.txt'
                    input message: "Do you want to apply the plan?",
                    parameters: [text(name: 'Plan', description: 'Please review the plan', defaultValue: plan)]
               }
           }
       }

        stage('Apply') {
            steps {
                sh "pwd;cd terraform/ ; terraform apply -input=false tfplan"
            }
        }
    }

  }
