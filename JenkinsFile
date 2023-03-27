pipeline{

    agent any 

        environment{

        DOCKERHUB_USERNAME = "anshuman123abc"
        APP_NAME = "gitops-argocd"
        IMAGE_TAG = "${BUILD_NUMBER}"
        IMAGE_NAME = "${DOCKERHUB_USERNAME}" + "/" + "${APP_NAME}"
        REGISTRY_CREDS = 'dockerhub'

    }


    stages{

        stage('Cleanup workspace'){


            steps{
                script{

                    cleanWs()
                }
            }
        }

        stage('Checkout SCM'){

            steps{

                script{

                    git credentialsId: "github",
                    url: 'https://github.com/anshumanhota1/gitops_argocd_project.git',
                    branch: 'main'
                }
            }

            }

            stage('Build docker image') {

                steps{

                    script{

                        docker_image = docker.build "${IMAGE_NAME}"
                    }
                }
            }
            stage('Push Docker image') {

                steps{

                    script{
                        docker.withRegistry('',REGISTRY_CREDS){

                            docker_image.push("$BUILD_NUMBER")
                            docker_image.push('latest')
                        }
                    }
                }
            }
            stage('delete Docker image') {

                steps{

                    script{
                        sh "docker rmi ${IMAGE_NAME}:${IMAGE_TAG}"
                        sh "docker rmi ${IMAGE_NAME}:latest"

                    }
                }
            }
            stage('Updating kubernetes deployment file') {

                steps{

                    script{

                        sh """
                        cat deployment.yml
                        sed -i 's/${APP_NAME}.*/${APP_NAME}:${IMAGE_TAG}/g' deployment.yml
                        cat deployment.yml
                        """
                    }
                }
            }
            

        }
    }
