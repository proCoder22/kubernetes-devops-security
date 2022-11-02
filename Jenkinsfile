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
    // stage('PIT mutation Tests') { 
    //   steps {
    //     sh "mvn org.pitest:pitest-maven:mutationCoverage"
    //   }
    //   post {
    //     always {
    //       pimutation mutationStatsFile: '**/target/pit-reports/**/mutations.xml'
    //     }
    //   }
    // }
    stage('Sonar SAST') {
      steps {
        sh "mvn clean verify sonar:sonar \
          -Dsonar.projectKey=numeric-application \
          -Dsonar.host.url=http://20.231.35.216:9000 \
          -Dsonar.login=sqp_45dc5b1c70d989b3419a80825da04bb8d484459f"
      }
    }
    stage('Docker Build and push') {
      steps {
        withDockerRegistry([credentialsId:"docker-hub", url:""]) {
          sh 'printenv'
          sh 'docker build -t vinit747/numeric-app:""$GIT_COMMIT"" .'
          sh 'docker push vinit747/numeric-app:""$GIT_COMMIT""'
        }
      }
    }
    stage('Kubernetes Deployment - DEV') {
      steps {
        withKubeConfig([credentialsId:'kubeconfig']) {
          sh "sed -i 's#replace#vinit747/numeric-app:${GIT_COMMIT}#g' k8s_deployment_service.yaml"
          sh "kubectl apply -f k8s_deployment_service.yaml"
        }
      }
    }
  }
}