pipeline {
    agent any
    stages {
        stage('Initial cleanup') {
            steps {
                dir(path: "${WORKSPACE}") {
                    deleteDir()
                }
            }
        }

        stage('Checkout SCM') {
            steps {
                git(branch: 'main', url: 'https://github.com/gashawgedef/php-todo.git')
            }
        }

        stage('Prepare Dependencies') {
            steps {
                // Move .env.sample to .env
                sh 'mv .env.sample .env'
                // Install composer dependencies
                sh 'composer install'
                // Run Laravel migrations and seed the database
                sh 'php artisan migrate'
                sh 'php artisan db:seed'
                // Generate a new application key
                sh 'php artisan key:generate'
            }
        }

        stage('Fix Permissions') {
            steps {
                // Ensure phpunit has execute permissions
                sh 'chmod +x ./vendor/bin/phpunit'
            }
        }

        stage('Execute Unit Tests') {
            steps {
                // Run PHPUnit tests
                sh './vendor/bin/phpunit --configuration phpunit.xml'
            }
        }
    }
}
