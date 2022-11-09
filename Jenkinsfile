import java.text.SimpleDateFormat
pipeline {
    agent any

     environment {
            registry = "fatmabe/devops-project"
            registryCredential = 'dockerHub'
            dockerImage = ''
     }

    stages {

        stage('Checkout GIT') {
            steps {
                echo 'Pulling...';
                git branch: 'nader',
                url : 'https://github.com/naderHAMAD/devopss.git'
            }
        }

      

        stage('MVN CLEAN'){
            steps{
                sh  'mvn clean'
            }
        }

        stage('MVN COMPILE'){
            steps{
                sh  'mvn compile'
            }
        }

        stage('MVN PACKAGE'){
              steps{
                  sh  'mvn package'
              }
        }
           
          stage('MVN SONARQUBE'){

                steps{
                          sh  'mvn sonar:sonar -Dsonar.login=admin -Dsonar.password=sonar'
                }
          }

           stage("Test JUnit /Mockito"){
                steps {
                            sh 'mvn test'
                }
          }

           stage('DOCKER COMPOSE') {
                steps {
                            sh 'docker-compose up -d --build'
                }
          }
        
         stage('Building our image') {
               steps{
                        script {
                            dockerImage = docker.build registry + ":latest"
                        }
               }
        }

         stage('Deploy our image') {
               steps {
                        script {
                            docker.withRegistry( '', registryCredential ) {
                                dockerImage.push()
                            }
                        }
               }
         }

         
    
         


       

    }

    post{

            success {
                mail to: "nader.hamad@esprit.tn",
                body: "${currentBuild.currentResult}: Job ${env.JOB_NAME} build ${env.BUILD_NUMBER}\n, More info at: ${env.BUILD_URL}",
                from: "nader.hamad@esprit.tn",
                subject: "Jenkins Build ${currentBuild.currentResult}: Job ${env.JOB_NAME}"
            }

            failure{
                mail to: "nader.hamad@esprit.tn",
                subject: "jenkins build:${currentBuild.currentResult}: ${env.JOB_NAME}",
                from: "nader.hamad@esprit.tn",
                body: "${currentBuild.currentResult}: Job ${env.JOB_NAME}\nMore Info can be found here: ${env.BUILD_URL}"
            }

            changed{
                mail to: "nader.hamad@esprit.tn",
                subject: "jenkins build:${currentBuild.currentResult}: ${env.JOB_NAME}",
                from: "nader.hamad@esprit.tn",
                body: "${currentBuild.currentResult}: Job ${env.JOB_NAME}\nMore Info can be found here: ${env.BUILD_URL}"
            }
        }
}
