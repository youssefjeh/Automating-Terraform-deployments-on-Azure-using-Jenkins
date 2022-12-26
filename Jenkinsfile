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
            withCredentials([azureServicePrincipal(credentialsId: 'azure-credentials',
                                    subscriptionIdVariable: 'SUBS_ID',
                                    clientIdVariable: 'CLIENT_ID',
                                    clientSecretVariable: 'CLIENT_SECRET',
                                    tenantIdVariable: 'TENANT_ID')]) {
    sh "az login --service-principal -u $CLIENT_ID -p $CLIENT_SECRET -t $TENANT_ID"
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

