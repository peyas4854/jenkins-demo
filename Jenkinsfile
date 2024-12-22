pipeline {
    agent any

    options {
        timeout(time: 30, unit: 'MINUTES') // Timeout after 30 minutes
        buildDiscarder(logRotator(numToKeepStr: '10')) // Keep only the last 10 builds
    }

    parameters {
        string(name: 'BRANCH_NAME', defaultValue: 'main', description: 'Branch to build')
        booleanParam(name: 'RUN_TESTS', defaultValue: true, description: 'Run tests?')
        choice(name: 'DEPLOY_ENV', choices: ['development', 'staging', 'production'], description: 'Deployment environment')
    }

    environment {
        // Define environment variables
        DEPLOY_PATH = '/var/www/my-app'
    }

    stages {
        stage('Checkout') {
            steps {
                script {
                    echo "Checking out branch: ${params.BRANCH_NAME}"
                    git branch: params.BRANCH_NAME, url: 'https://github.com/peyas4854/jenkins-demo.git'
                }
            }
        }
        stage('Build') {
            steps {
                echo 'Building the application...'
                sh './build.sh' // Replace with actual build script/command
            }
        }
        stage('Test') {
            when {
                expression { params.RUN_TESTS } // Only run tests if RUN_TESTS is true
            }
            steps {
                echo 'Running tests...'
                parallel(
                    UnitTests: {
                        sh './run-unit-tests.sh' // Replace with actual test script/command
                    },
                    IntegrationTests: {
                        sh './run-integration-tests.sh' // Replace with actual test script/command
                    }
                )
            }
        }
        stage('Deploy') {
            steps {
                echo "Deploying to ${params.DEPLOY_ENV} environment..."
                sh "./deploy.sh ${params.DEPLOY_ENV}" // Replace with actual deployment script
            }
        }
    }

    post {
        always {
            echo 'Cleaning up...'
            cleanWs() // Clean workspace
        }
        success {
            echo 'Pipeline succeeded!'
            mail to: 'team@example.com',
                 subject: "Pipeline Succeeded: ${env.JOB_NAME}",
                 body: "The pipeline for job ${env.JOB_NAME} has completed successfully."
        }
        failure {
            echo 'Pipeline failed!'
            mail to: 'team@example.com',
                 subject: "Pipeline Failed: ${env.JOB_NAME}",
                 body: "The pipeline for job ${env.JOB_NAME} has failed. Please check the logs."
        }
    }
}
