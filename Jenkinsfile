pipeline {
  agent any
  parameters {
    choice(choices: ['apply' , 'destroy'],name: 'ACTION')
  }
  stages {
    stage('Authenticate with Azure') {
      steps {
        sazureLogin credentialsId: 'azure-credentials'
      }
    }
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
            sh "terraform $ACTION --auto-approve"
          }
        }
      }
    }
  }
}

