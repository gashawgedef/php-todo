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
        sh 'mv .env.sample .env'
        sh 'composer install'
        sh 'php artisan migrate '
        sh 'php artisan db:seed'
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

    stage('Run Unit Tests') {
            steps {
                // Execute PHPUnit tests using the downloaded PHPUnit version
                sh './phpunit --configuration phpunit.xml'
            }
        }

  }
}
