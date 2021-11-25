pipeline {
    agent any
    options {
        disableConcurrentBuilds()
        timeout(time: 10, unit: 'MINUTES')
    }
    tools {
        maven 'apache-maven-3.8.2'
        jdk 'adoptopenjdk-hotspot-jdk11-latest'
    }
    stages {
        stage('Build') {
            steps {
                sh 'mvn clean verify -Prelease -B'
            }
        }
    }
}