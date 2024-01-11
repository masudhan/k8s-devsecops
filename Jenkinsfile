pipeline {
  agent any
  environment {
        tag = sh(returnStdout: true, script: "git rev-parse --short=10 HEAD").trim()
    }

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
      } 
      stage('Mutation-Tests-PIT') {
        steps {
          sh "mvn org.pitest:pitest-maven:mutationCoverage"
        }
      } 
      stage('Sonarqube - SAST') {
        steps{
          withSonarQubeEnv('Sonarqube'){
            sh "mvn clean verify sonar:sonar -Dsonar.projectKey=numeric-application -Dsonar.projectName='numeric-application' -Dsonar.host.url=http://10.0.2.15:32065 -Dsonar.token=sqp_32d65d1c31161c07eb99d308aa679c5ce95ce08a"
          }
          timeout(time: 2, unit: 'MINUTES'){
            script {
              waitForQualityGate abortPipeline: true
            }
          }
        }
      }
      stage('Vulnerability Scan - Docker') {
        steps {
          sh "mvn dependency-check:check"
        }
      } 
      stage('Docker Build & Push ') {
            steps {
              withDockerRegistry([credentialsId: "docker-creds", url: ""]){
                sh "printenv" //list out all the jenkins env variables
                sh "docker build -t chmadhus/numeric-app:${tag} ."
                sh "docker push chmadhus/numeric-app:${tag}"
              }
            }
      }
      stage('Kubernetes Deployment - DEV') {
            steps {
              withKubeConfig([credentialsId: 'kubeconfig']){
                sh "sed -i 's#replace#chmadhus/numeric-app:${tag}#g' k8s_deployment_service.yaml"
                sh "kubectl apply -f k8s_deployment_service.yaml"
              }
            }
      }
    }
    post{
      always{
        junit 'target/surefire-reports/*.xml'
        jacoco execPattern: 'target/jacoco.exec'
        // pitmutation mutationStatsFile: '**/target/pit-reports/**/mutations.xml'
        dependencyCheckPublisher pattern: 'target/dependency-check-report.xml'
      }
    }
}
