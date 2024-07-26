pipeline {
    parameters {
        booleanParam(name: 'autoApprove', defaultValue: false, description: 'Automatically run apply after generating plan?')
    }
    environment {
        AWS_ACCESS_KEY_ID     = credentials('AWS_ACCESS_KEY_ID')
        AWS_SECRET_ACCESS_KEY = credentials('AWS_SECRET_ACCESS_KEY')
    }
    agent any
    tools {
        terraform 'Terraform' // Nom que vous avez donné dans la configuration globale
    }
    stages {
        stage('checkout') {
            steps {
                script {
                    dir("terraform") {
                        git 'https://github.com/douniaharag/Terraform_aws.git'
                    }
                }
            }
        }
        stage('Plan') {
            steps {
                sh 'terraform init'
                sh "terraform plan -out tfplan"
                sh 'terraform show -no-color tfplan > tfplan.txt'
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
                    input message: "Do you want to apply the plan?", parameters: [text(name: 'Plan', description: 'Please review the plan', defaultValue: plan)]
                }
            }
        }
        stage('Apply') {
            steps {
                sh "terraform apply -input=false tfplan"
            }
        }
    }
}