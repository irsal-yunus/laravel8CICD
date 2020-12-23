pipeline {
    agent any
    stages {
        stage("Build") {
           /* environment {
                DB_HOST = credentials("127.0.0.1")
                DB_DATABASE = credentials("laravel")
                DB_USERNAME = credentials("root")
                DB_PASSWORD = credentials("")
            } */
            steps {
                bat 'php --version'
                bat 'composer --version'              
                bat 'composer install'                
                bat 'copy .env.example .env'
                bat 'echo DB_HOST=${DB_HOST} >> .env'
                bat 'echo DB_USERNAME=${DB_USERNAME} >> .env'
                bat 'echo DB_DATABASE=${DB_DATABASE} >> .env'
                bat 'echo DB_PASSWORD=${DB_PASSWORD} >> .env'
                bat 'php artisan key:generate'
                bat 'copy .env .env.testing'
                bat 'php artisan migrate'
            }
        }
        stage("Unit test") {
            steps {
                bat 'php artisan test'
            }
        }
        stage("Code coverage") {
            steps {
                bat "vendor/bin/phpunit --coverage-html 'reports/coverage'"
            }
        }
        stage("Static code analysis larastan") {
            steps {
                bat "vendor/bin/phpstan analyse --memory-limit=2G"
            }
        }
        stage("Static code analysis phpcs") {
            steps {
                bat "vendor/bin/phpcs"
            }
        }
        stage("Docker build") {
            steps {
                bat "docker build -t laravel8cicd -f Dockerfile ."
            }
        }
        stage("Docker push") {
          /*  environment {
                DOCKER_USERNAME = credentials("ichalapyel")
                DOCKER_PASSWORD = credentials("irsal12345")                
            } */
            steps {
                bat "docker login -u ichalapyel -p irsal12345"
                bat "docker tag laravel8cicd ichalapyel/laravel8cicd "
                bat "docker push ichalapyel/laravel8cicd"
            }
        }
        stage("Deploy to staging") {
            steps {
                bat "docker run -d --rm -p 80:80 --name laravel8cicd laravel8cicd"
            }
        }
        stage("Acceptance test curl") {
            steps {
                sleep 20
                bat "./acceptance_test.sh"
            }
        }
        stage("Acceptance test codeception") {
            steps {
                bat "vendor/bin/codecept run"
            }
            post {
                always {
                    bat "docker stop laravel8cicd"
                }
            }
        } 
    }
}
