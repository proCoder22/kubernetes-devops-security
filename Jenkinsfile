pipeline {
  agent any

  stages {
    stage('Build Artifact') {
          steps {
            sh "mvn clean package -DskipTests=true"
            archive 'target/*.jar' 
          }
      }
    stage('Unit Test') {
        steps {
          sh "mvn test" 
        }
        post {
          always {
            junit 'target/surefire-reports/*.xml'
            jacoco execPattern: 'targ et/jacoco.exec'
          }
        }
    }
    stage('Docker Build and push') {
      steps {
        sh 'printenv'
        sh 'docker build -t vinit747/numeric-app:""$GIT_COMMIT"" .'
        sh 'docker push vinit747/numeric-app:""$GIT_COMMIT""'
      }
    }
  }
}