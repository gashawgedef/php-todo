pipeline {
    agent any

    stages {
        stage('Initial cleanup') {
            steps {
                dir("${WORKSPACE}") {
                    deleteDir()
                }
            }
        }

        stage('Checkout SCM') {
            steps {
                git branch: 'main', url: 'https://github.com/StegTechHub/php-todo.git'
            }
        }

        stage('Prepare Dependencies') {
    steps {
        script {
            // Check if .env.sample exists before renaming
            if (fileExists('.env.sample')) {
                sh 'mv .env.sample .env'
            } else {
                echo ".env.sample file does not exist."
            }
            sh 'composer install'
            sh 'php artisan migrate'
            sh 'php artisan db:seed'
            sh 'php artisan key:generate'
        }
    }
}
    }
}
ls -la
