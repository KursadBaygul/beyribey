pipeline {
    agent any

    environment {
        REPO_DIR = "${WORKSPACE}/repo"
        BUILD_DIR = "${WORKSPACE}/build-output"
        TARGET_HOST = "root@91.98.195.255"
        TARGET_DIR = "/var/www/beyribey"
        SSH_CRED = "ssh-target-91"
    }

    stages {

        stage('Checkout') {
            steps {
                script {
                    sh "rm -rf ${REPO_DIR}"

                    withCredentials([usernamePassword(
                        credentialsId: 'github-credentials',
                        usernameVariable: 'GIT_USERNAME',
                        passwordVariable: 'GIT_PASSWORD'
                    )]) {
                        sh """
                            git clone --branch main https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/beyribey.git ${REPO_DIR}
                        """
                    }
                }
            }
        }

        stage('Install & Build (YARN)') {
            steps {
                sh """
                    rm -rf ${BUILD_DIR}

                    cd ${REPO_DIR}

                

                    mv * ${BUILD_DIR}
                """
            }
        }

        stage('Upload to Target Server') {
            steps {
                sshagent(credentials: [SSH_CRED]) {
                    sh """
                        echo ">>> Removing old client files"
                        ssh -o StrictHostKeyChecking=no ${TARGET_HOST} "rm -rf ${TARGET_DIR}/*"

                        echo ">>> Copying new build"
                        scp -o StrictHostKeyChecking=no -r ${BUILD_DIR}/* ${TARGET_HOST}:${TARGET_DIR}/
                    """
                }
            }
        }

        stage('Cleanup') {
            steps {
                sh """
                    rm -rf ${REPO_DIR}
                    rm -rf ${BUILD_DIR}
                """
            }
        }
    }

    post {
        success {
            echo "✔ React Client deployed successfully!"
        }
        failure {
            echo "❌ React Client deployment failed!"
        }
    }
}
