@Library('java_shared') _
pipeline{
    agent any
    tools {
        maven 'maven3'
        jdk 'java11'
        dependencyCheck name: 'DPC'
    }
    options {
        buildDiscarder(logRotator(numToKeepStr: '2'))
    }
    parameters{
        choice(name: 'action', choices: 'create\ndelete', description: 'Choose Create/Destroy')
    }
    
    stages{
        stage("SCM Checkout"){
            when { expression { params.action == 'create' } }
            steps{
                gitCheck(
                     branch: 'main',
                     url: 'https://github.com/devops-aws-linux/Petclinic.git'
                )
            }
        }
        stage("Maven Clean Compile"){
            when { expression { params.action == 'create' } }
            steps{
                script{
                    mavenCompile()
                }
            }
        }
        stage("Maven Test"){
            when { expression { params.action == 'create' } }
            steps{
                script{
                    mavenTest()
                }
            }
        }
        stage('OWASP Dependency-Check') {
            steps {
                script {
                    // You can now use the OWASP Dependency-Check tool
                    sh "${tool name: 'DPC', type: 'Tool'}/bin/dependency-check.sh --version"
                }
            }
        }
        
    }
}