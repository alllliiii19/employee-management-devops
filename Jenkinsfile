pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                echo 'Source code will be checked from github.'
            }
    }

    stage('Build') {
        steps {
            echo 'Building the application.'
            sh 'mvn clean package'
        }
    }
  } 

}