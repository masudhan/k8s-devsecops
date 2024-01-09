pipeline {
  agent any

  stages {
      stage('Build Artifact') {
            steps {
              sh "mvn clean package -DskipTests=true"
              archive 'target/*.jar' //so that they can be downloaded later
            }
        } 
      stage('Unit Testing') {
            steps {
              sh "mvn test"
            }
            post {
              always {
                junit 'target/surefire-reports/*.xml'
                jacoco execPattern: 'target/jacoco.exec'
              }
            }
      } 
      stage('Docker Build & Push ') {
            steps {
              withDockerRegistry([credentialId: "docker-creds", url: ""]){
                sh "printenv" //list out all the jenkins env variables
                sh 'docker build -t chmadhus/numeric-app:""$GIT_COMMIT""'
                sh 'docker push chmadhu/numeric-app:""$GIT_COMMIT""'
              }
            }
      }
    }
}
