
pipeline {

    agent any

    parameters{

        choice(name: 'action', choices: 'create\ndelete', description: 'Choose create/Destroy')
        string(name: 'ImageName', description: "name of the docker build", defaultValue: 'javapp')
        string(name: 'ImageTag', description: "tag of the docker build", defaultValue: 'v1')
        string(name: 'DockerHubUser', description: "name of the Application", defaultValue: 'vikashashoke')
    }


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

//        stage('Quality Gate status') {
//
//            steps{
//
//                script{
//                    waitForQualityGate abortPipeline: false, credentialsId: 'sonar-token'
//                }
//            }
//        }


        stage('Docker build and docker push to nexus repo') {

            steps {

                script {
                    script {

                        withCredentials([string(credentialsId: 'nexus_passwd', variable: 'nexus_creds')]) {

                            docker build t 3.83 .66 .55: 8083 / springapp : $ { VERSION }.

                                    docker login - u admin -p $nexus_creds 3.83 .66 .55: 8083

                            docker push 3.83 .66 .55 : 8083 / springapp : $ { VERSION }

                            docker rmi 3.83 .66 55 : 8083 / springapp : $ { VERSION }

                        }
                    }

                }
            }
        }

        stage('Docker Image Build'){
            when { expression {  params.action == 'create' } }
            steps{
                script{

                    dockerBuild("${params.ImageName}","${params.ImageTag}","${params.DockerHubUser}")
                }
            }
        }



        stage('connecting to k8s cluster'){
            steps{
                script{
                    withCredentials([kubeconfigFile(credentialsId: 'kubernetes-config', variable: 'KUBECONFIG')]) {
                        dir ("kubernetes/"){
                            sh 'helm list'
                            sh 'helm upgrade --install --set image.repository="nexus_ip:8083/springapp" --set image.tag="${VERSION}" myjavaapp myapp/ '
                        }
                    }
                }
            }
        }

        post {
            always {
                mail bcc: '', body: "<br>Project: ${env.JOB_NAME} <br>Build Number: ${env.BUILD_NUMBER} <br> URL de build: ${env.BUILD_URL}", cc: '', charset: 'UTF-8', from: '', mimeType: 'text/html', replyTo: '', subject: "${currentBuild.result} CI: Project name -> ${env.JOB_NAME}", to: "vineshbagul93@gmail.com";
            }
        }


        stage{
            steps
                    {
                        script{
                            curl -u admin:$nexus_password http://nexus_machine_ip:8081/repository/helm-hosted/ --upload-file myapp-${helmversion}.tgz -v
                        }
                    }
        }

        Stage('Identifying Misconfug using datree in helm chart')
                {
                    steps{
                        script{
                            dir('kubernetes/myapp'){
                                sh'helm datree test .'
                            }
                        }
                    }
                }


    }

}




