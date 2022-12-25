@Library('jenkins-SL') 
def gv

pipeline {
  agent any
  tools {
    maven 'Maven'
  }
  parameters {
    choice(choices: ['apply' , 'destroy'],name: 'ACTION')
  }
  stages {

    stage("build Jar") {
      
      steps {
        script{
            buildJar()
        }
      }
    }

    stage("build docker image") {
      
      steps {
        script{
          buildImg()
        }
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
            sh 'az login'
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
    
    stage("deploy to ACR_hub") {
      
      steps {
        script{
            deployApp()
        }
      }
    }
  }
}

