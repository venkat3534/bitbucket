pipeline {
    agent any

    environment {
        DEPLOY_USER = "ubuntu"       // User on web server
        DEPLOY_SERVER = "43.205.131.100"   // Web server IP
        DEPLOY_PATH = "/var/www/html/mysite"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'master', url: 'https://venkat3534@bitbucket.org/venkat3534/drupal-site.git'
            }
        }

        stage('Build') {
            steps {
                sh 'composer install --no-dev'
            }
        }

        stage('Deploy') {
            steps {
                sshagent (credentials: ['webserver-ssh-key']) {
                    sh """
                        rsync -avz --delete \
                        ./ ${DEPLOY_USER}@${DEPLOY_SERVER}:${DEPLOY_PATH}
                    """
                }
            }
        }

        stage('Post-Deploy') {
            steps {
                sshagent (credentials: ['webserver-ssh-key']) {
                    sh """
                        ssh ${DEPLOY_USER}@${DEPLOY_SERVER} "cd ${DEPLOY_PATH} && drush cr && drush updb -y"
                    """
                }
            }
        }
    }
}
