
pipeline {

    agent any

    stages {

        stage('Sonarqube quality check') {

            agent {

                docker {
                    image 'maven'
                }
            }

            steps {

                script {
                    withSonarQubeEnv(credentialsId: 'sonar-token') {
                        sh 'mvn clean package sonar:sonar'
                    }

                }
            }


         }

        stage('Quality Gate status') {

            steps {

                script {
                    waitForQualityGate abortPipeline: true, credentialsId: 'sonar-token'
                }
            }
        }


        //                stage('Docker build and docker push to nexus repo')
//
//                    steps{
//
//                        script {
//
//                        }
//                    }
//
//               }





    }

}