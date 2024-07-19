pipeline {
    agent any
    environment {
        APP_NAME = "netflix-clone-pipeline"
        GIT_USERNAME = "onyima101"
        GIT_EMAIL = "onyima_101@yahoo.com"
        GIT_REPONAME = "netflix-clone-gitops"      
    }
    stages {
        stage('clean workspace') {
            steps {
                cleanWs()
            }
        }
        stage('Checkout from SCM') {
            steps {
                git branch: 'main', credentialsId: 'github', url: 'https://github.com/onyima101/netflix-clone.git'
            }
        }
        stage("Update the Deployment Tags") {
            steps {
                script {
                    dir('Kubernetes') {
                        sh """
                            cat deployment.yml
                            sed -i 's/${APP_NAME}.*/${APP_NAME}:${IMAGE_TAG}/g' deployment.yml
                            cat deployment.yml
                        """
                    }
                }
            }
        }
        stage("Push the changed deployment file to GitHub") {
            steps {
                script {
                    dir('Kubernetes') {
                        sh """
                            git config --global user.name "${GIT_USERNAME}"
                            git config --global user.email "${GIT_EMAIL}"
                            git add deployment.yml
                            git commit -m "Updated Deployment Manifest"
                        """
                        withCredentials([gitUsernamePassword(credentialsId: 'github', gitToolName: 'Default')]) {
                            sh "git push https://github.com/${GIT_USERNAME}/${GIT_REPONAME} HEAD:main"
                        }
                    }    
                }
            }
        }
    }
}
