pipeline {
  agent any
  parameters {
    choice(choices: ['apply' , 'destroy'],name: 'ACTION')
  }
  stages {
    stage("terraform init") {
      steps {
        script {
          dir('terraform'){
            sh "terraform init"
          }
        }
      }
    }
    stage("terraform validate") {
      steps {
        script {
          dir('terraform'){
            sh 'terraform validate'
          }
        }
      }
    }

    stage("terraform plan") {
      steps {
        script {
          dir('terraform'){
            withCredentials([azureServicePrincipal(credentialsId: 'azure-credentials')]) {
              echo "My client id is $AZURE_CLIENT_ID"
              echo "My client secret is $AZURE_CLIENT_SECRET"
              echo "My tenant id is $AZURE_TENANT_ID"
              echo "My subscription id is $AZURE_SUBSCRIPTION_ID"
            }
            sh 'terraform plan'
          }
        }
      }
    }

    stage("Terraform Approval") {
       steps {
        script {
          def userInput = input(id: 'confirm', message: 'Approve Terraform ?', parameters: [
            [$class: 'BooleanParameterDefinition', defaultValue: false, description: 'Approve Terraform', name: 'confirm']
          ])
        }
      }
    }

    stage("Terraform Action") {
      steps {
        script {
          dir('terraform') {
            sh 'terraform $ACTION --auto-approve'
          }
        }
      }
    }
  }
}

