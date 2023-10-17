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
                script {
                    // Use the OWASP Dependency-Check tool to scan
                    def dependencyCheck = tool name: 'dependency-check-8', type: 'DependencyCheckTool'
                    sh "${dependencyCheck}/bin/dependency-check.sh --scan target/"
                }

                // Publish the Dependency-Check report
                step([
                    $class: 'DependencyCheckPublisher',
                    pattern: 'dependency-check-report.xml'
                ])
            }
        }
    }
}