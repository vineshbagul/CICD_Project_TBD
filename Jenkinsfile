
pipeline {

        agent any

        stages {

                      stage('Sonarqube quality status'){

                            agent{

                                docker {
                                   image 'maven'
                                }
                            }

                          steps{

                              script{
                                        withSonarQubeEnv(credentialsId: 'sonar-token') {

                                            sh 'mvn clean package sonar:sonar'
                                        }

                              }
                          }

                      }
               }

}