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
                
                // Ensure the .env file is populated (for example)
                sh 'echo DB_CONNECTION=mysql >> .env'
                sh 'echo DB_HOST=127.0.0.1 >> .env'
                sh 'echo DB_PORT=3306 >> .env'
                sh 'echo DB_DATABASE=your_database_name >> .env'
                sh 'echo DB_USERNAME=your_username >> .env'
                sh 'echo DB_PASSWORD=your_password >> .env'
                
                // Install composer dependencies
                sh 'composer install'
                
                // Run Laravel migrations and seed the database
                sh 'php artisan migrate --force'
                sh 'php artisan db:seed --force'
                
                // Generate a new application key
                sh 'php artisan key:generate'
            }
        }

         stage('Setup Laravel Directories') {
            steps {
                dir("${WORKSPACE}") {
                    // Ensure Laravel storage directories exist
                    sh 'mkdir -p storage/framework/sessions'
                    sh 'mkdir -p storage/framework/cache'
                    sh 'chmod -R 777 storage' // Adjust permissions as needed for your environment
                }
            }
        }

        stage('Download PHPUnit') {
            steps {
                sh 'wget -O phpunit https://phar.phpunit.de/phpunit-${PHPUNIT_VERSION}.phar'
                sh 'chmod +x phpunit'
            }
        }

        stage('Execute Unit Tests') {
            steps {
                // Run PHPUnit tests
                sh './vendor/bin/phpunit --configuration phpunit.xml --verbose'
            }
        }
    }
}
