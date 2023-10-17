@Library('java_shared') _
pipeline{
    agent any
    tools {
        maven 'maven3'
        jdk 'java11'
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
        stage('OWASP Dependency-Check Vulnerabilities') {
            when { expression { params.action == 'create' } }
            steps {
                script{
                    dpCheck(
                    'DPC', '''
                    -o './'
                    -s './'
                    -f 'ALL'
                    --prettyPrint''', 
                    'dependency-check-report.xml')
                }
            }
        }
    }
}