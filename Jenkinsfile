pipeline {
    agent any
    
    environment {
        PHPUNIT_VERSION = '9.5'
    }
    
    tools {
        sonar 'SonarQubeScanner'
    }
    
    stages {
        stage('Initial Cleanup') {
            steps {
                deleteDir()  // Delete the current workspace
            }
        }

        stage('Checkout SCM') {
            steps {
                git branch: 'main', credentialsId: 'gashity_token', url: 'https://github.com/gashawgedef/php-todo.git'
            }
        }

        stage('Prepare Dependencies') {
            steps {
                sh 'mv .env.sample .env'
                sh 'composer install'
                sh 'php artisan migrate'
                sh 'php artisan db:seed'
                sh 'php artisan key:generate'
            }
        }

        stage('Setup Laravel Directories') {
            steps {
                sh '''
                mkdir -p storage/framework/sessions storage/framework/cache
                chmod -R 777 storage
                '''
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
                sh './phpunit --configuration phpunit.xml'
            }
        }

        stage('Download PHPLOC') {
            steps {
                sh '''
                mkdir -p build/logs
                wget -O phploc.phar https://phar.phpunit.de/phploc.phar
                chmod +x phploc.phar
                '''
            }
        }

        stage('Code Analysis') {
            steps {
                sh './phploc.phar app/ --log-csv build/logs/phploc.csv'
                archiveArtifacts artifacts: 'build/logs/phploc.csv', allowEmptyArchive: true
            }
        }

        stage('SonarQube Quality Gate') {
            environment {
                scannerHome = tool 'SonarQubeScanner'
            }
            steps {
                withSonarQubeEnv('sonarqube') {
                    sh "${scannerHome}/bin/sonar-scanner"
                }
            }
        }

        stage('Package Artifact') {
            steps {
                sh 'zip -qr php-todo.zip *'
                archiveArtifacts artifacts: 'php-todo.zip', allowEmptyArchive: false
            }
        }

        stage('Upload Artifact to Artifactory') {
            steps {
                script {
                    def server = Artifactory.server 'artifactory-server'
                    def uploadSpec = """{
                        "files": [{
                            "pattern": "php-todo.zip",
                            "target": "todo-dev-local/php-todo${env.BUILD_NUMBER}/",
                            "props": "type=zip;status=ready"
                        }]
                    }"""
                    server.upload spec: uploadSpec
                }
            }
        }

        stage('Deploy to Dev Environment') {
            steps {
                build job: 'ansible-config-mgt/main', parameters: [[$class: 'StringParameterValue', name: 'env', value: 'dev']], propagate: false, wait: true
            }
        }
    }
}
