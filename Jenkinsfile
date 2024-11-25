pipeline {
    agent any
    
    environment {
        PHPUNIT_VERSION = '9.5'  // Define the PHPUnit version if it's not already defined elsewhere
    }

    stages {
        stage('Initial cleanup') {
            steps {
                dir(path: "${WORKSPACE}") {
                    deleteDir()  // Delete workspace contents
                }
            }
        }

        stage('Checkout SCM') {
            steps {
               git branch: 'main', credentialsId: 'gashity_token', url: 'https://github.com/gashawgedef/php-todo.git'

            }
        }

        stage('Prepare Dependencies') {
            steps {
                sh 'mv .env.sample .env'  // Rename .env.sample to .env
                sh 'composer install'  // Install Composer dependencies
                sh 'php artisan migrate'  // Run Laravel migrations
                sh 'php artisan db:seed'  // Seed the database
                sh 'php artisan key:generate'  // Generate Laravel application key
            }
        }

        stage('Setup Laravel Directories') {
            steps {
                dir("${WORKSPACE}") {
                    // Ensure Laravel storage directories exist
                    sh 'mkdir -p storage/framework/sessions'
                    sh 'mkdir -p storage/framework/cache'
                    sh 'chmod -R 777 storage'  // Set permissions for storage directory
                }
            }
        }

        stage('Download PHPUnit') {
            steps {
                sh 'wget -O phpunit https://phar.phpunit.de/phpunit-${PHPUNIT_VERSION}.phar'  // Download PHPUnit
                sh 'chmod +x phpunit'  // Make the PHPUnit file executable
            }
        }

        stage('Run Unit Tests') {
            steps {
                // Run PHPUnit tests using the downloaded PHPUnit version
                sh './phpunit --configuration phpunit.xml'
            }
        }
        stage('Download PHPLOC') {
            steps {
                sh 'wget -O phploc.phar https://phar.phpunit.de/phploc.phar'
                sh 'chmod +x phploc.phar'
            }
        }
       stage('Code Analysis') {
          steps {
                sh 'phploc app/ --log-csv build/logs/phploc.csv'

          }
    }
    }
}
