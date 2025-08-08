pipeline {
    agent any

    environment {
        DEPLOY_USER = "ubuntu"       // Webserver user
        DEPLOY_SERVER = "65.0.120.115"   // Webserver IP
        DEPLOY_PATH = "/var/www/html/mysite"  // Webserver Path
        DB_BACKUP_DIR = "/home/ubuntu/db_backups"
        DB_NAME       = "drupaldb"
        DB_USER       = "drupaluser"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'master', url: 'https://github.com/venkat3534/bitbucket.git'
            }
        }

        stage('Deploy') {
            steps {
                sshagent (credentials: ['webserver-ssh-key']) {
                    sh """
                        mkdir -p ~/.ssh
                        ssh-keyscan -H ${DEPLOY_SERVER} >> ~/.ssh/known_hosts
                        rsync -avz --exclude 'sites/default/files' ./mysite/ ubuntu@${DEPLOY_SERVER}:${DEPLOY_PATH}
                    """
                }
            }
        }
        stage('Post-Deploy') {
            steps {
                withCredentials([string(credentialsId: 'db-password', variable: 'DB_PASS_ID')]) {
                    sshagent (credentials: ['webserver-ssh-key']) {
                        sh """
                            ssh ${DEPLOY_USER}@${DEPLOY_SERVER} "
                                mkdir -p ${DB_BACKUP_DIR} &&
                                mysqldump -u ${DB_USER} -p${DB_PASS_ID} ${DB_NAME} > ${DB_BACKUP_DIR}/db_backup_\$(date +%F_%H-%M-%S).sql &&
                                cd ${DEPLOY_PATH} &&
                                composer install --no-dev &&
                                ./vendor/bin/drush cr &&
                                ./vendor/bin/drush updb -y
                            "
                        """
                    }
                }
            }
        }
    }
}
