pipeline {
    agent any

    environment {
        DEPLOY_USER = "ubuntu"       // Webserver user
        DEPLOY_SERVER = "65.0.120.115"   // Webserver IP
        DEPLOY_PATH = "/var/www/html/mysite"  // Webserver Path
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
                        echo "Deployed on date" > version.txt
                        rsync -avz --exclude ./mysite/ ubuntu@${DEPLOY_SERVER}:${DEPLOY_PATH}
                    """
                }
            }
        }

        stage('Post-Deploy') {
            steps {
                sshagent (credentials: ['webserver-ssh-key']) {
                    sh """
                        ssh ubuntu@${DEPLOY_SERVER} "cd ${DEPLOY_PATH} && composer install --no-dev && ./vendor/bin/drush cr && ./vendor/bin/drush updb -y"
                    """
                }
            }
        }
    }
}
