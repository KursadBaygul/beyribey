pipeline {
  agent any

  environment {
    BUILD_DIR   = "${WORKSPACE}/build-output"
    TARGET_HOST = "root@91.98.195.255"
    TARGET_DIR  = "/var/www/beyribey"
    SSH_CRED    = "ssh-target-91"
  }

  stages {

    stage('Prepare') {
      steps {
        sh """
          rm -rf "${BUILD_DIR}"
          mkdir -p "${BUILD_DIR}"

          # Sadece statik dosyaları al (kendini kopyalama hatası biter)
          cp -a index.html assets "${BUILD_DIR}/"
        """
      }
    }

    stage('Upload to Target Server') {
      steps {
        sshagent(credentials: [SSH_CRED]) {
          sh """
            echo ">>> Cleaning target dir"
            ssh -o StrictHostKeyChecking=no ${TARGET_HOST} "mkdir -p ${TARGET_DIR} && rm -rf ${TARGET_DIR}/*"

            echo ">>> Uploading static files"
            scp -o StrictHostKeyChecking=no -r "${BUILD_DIR}/"* ${TARGET_HOST}:${TARGET_DIR}/
          """
        }
      }
    }

    stage('Cleanup') {
      steps {
        sh """
          rm -rf "${BUILD_DIR}"
        """
      }
    }
  }

  post {
    success { echo "✔ Static site deployed successfully!" }
    failure { echo "❌ Deployment failed!" }
  }
}
