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
                git branch: 'master', url: 'https://github.com/venkat3534/bitbucket.git'
            }
        }

        stage('Deploy') {
            steps {
                sshagent (credentials: ['webserver-ssh-key']) {
                    sh """
                        mkdir -p ~/.ssh
                        ssh-keyscan -H 43.205.131.100 >> ~/.ssh/known_hosts
                        rsync -avz --delete ./mysite/ ubuntu@43.205.131.100:/var/www/html/mysite
                    """
                }
            }
        }

        stage('Post-Deploy') {
            steps {
                sshagent (credentials: ['webserver-ssh-key']) {
                    sh """
                        ssh ubuntu@43.205.131.100 "cd /var/www/html/mysite && composer install --no-dev && ./vendor/bin/drush cr && ./vendor/bin/drush updb -y"
                    """
                }
            }
        }
    }
}
