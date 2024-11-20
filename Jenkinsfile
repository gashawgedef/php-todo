pipeline {
    agent any

    environment {
        // Add any environment variables here if needed
    }

    stages {
        stage('Initial cleanup') {
            steps {
                script {
                    // Remove everything in the workspace before starting
                    deleteDir()
                }
            }
        }

        stage('Checkout SCM') {
            steps {
                // Checkout the code from the repository
                git branch: 'main', url: 'https://github.com/gashawgedef/php-todo.git'
            }
        }

        stage('Prepare Dependencies') {
            steps {
                script {
                    // Move the sample environment file to .env
                    sh 'mv .env.sample .env'

                    // Install PHP dependencies
                    sh 'composer install'

                    // Run database migrations
                    sh 'php artisan migrate'

                    // Seed the database
                    sh 'php artisan db:seed'

                    // Generate application key
                    sh 'php artisan key:generate'
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline executed successfully!'
        }
        failure {
            echo 'Pipeline execution failed!'
        }
    }
}
