pipeline {
    agent any
    
    stages {
        stage('Checkout code') {
            steps {
                checkout scm
            }
        }
        
        stage('Set up JDK') {
            steps {
                tool name: 'JDK 11', type: 'jdk'
            }
        }
        
        stage('Build with Maven') {
            steps {
                bat 'mvn -B package pom.xml'
            }
        }
        
        stage('Store artifact') {
            steps {
                bat 'mkdir artifacts'
                bat 'copy token\\target\\*.jar artifacts\\'
            }
        }
        
        stage('Commit and push artifact') {
            steps {
                script {
                    def gitActor = env.GITHUB_ACTOR
                    gitUserName(gitActor)
                    gitUserEmail("${gitActor}@users.noreply.github.com")
                    checkout([$class: 'GitSCM', branches: [[name: '*/main']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'YOUR_CREDENTIALS_ID', url: 'YOUR_GITHUB_REPO_URL']]])
                    bat 'git add artifacts\\'
                    bat 'git commit -m "Add built artifact"'
                    bat 'git push origin HEAD:refs/heads/main'
                }
            }
        }
        
        stage('Execute Java Program') {
            steps {
                bat 'java -cp artifacts\\restdemo-0.0.1-SNAPSHOT.jar com.google.firebase.samples.config.Configure'
            }
        }
    }
}
