pipeline {
    agent any
    parameters {
        booleanParam(name: 'RUN_DEPLOY', defaultValue: true, description: 'Should we deploy?')
        choice(name: 'ENV', choices: ['dev', 'staging', 'prod'], description: 'Select deployment environment')
    }

    stages {
        stage('Build') {
            steps {
                echo 'Building the application...'
                sh 'sleep 2'
            }
        }

        stage('Test in Parallel') {
            parallel {
                stage('Unit Tests') {
                    when {
                        expression { currentBuild.currentResult == 'SUCCESS' } // Run only if Build succeeds
                    }
                    steps {
                        echo 'Running unit tests...'
                        sh 'sleep 3'
                    }
                }
                stage('Integration Tests') {
                    steps {
                        echo 'Running integration tests...'
                        sh 'sleep 3'
                    }
                }
            }
        }

        stage('Simulate Testing') {
            parallel {
                stage('Linux Test') {
                    steps {
                        echo 'Simulating Linux tests...'
                        sh 'sleep 2'
                    }
                }
                stage('Windows Test') {
                    steps {
                        echo 'Simulating Windows tests...'
                        sh 'sleep 2'
                    }
                }
            }
        }

        stage('Test') {
            steps {
                sh 'echo "All tests passed!" > results.txt'
                archiveArtifacts artifacts: 'results.txt', fingerprint: true
            }
        }

        stage('Approval') {
            steps {
                timeout(time: 2, unit: 'MINUTES') { // Auto-fail if no approval within 2 minutes
                    input message: "Do you want to proceed with deployment?"
                }
            }
        }

        stage('Deploy') {
            when {
                expression { return params.RUN_DEPLOY } 
            }
            steps {
                echo "Deploying the application to environment: ${params.ENV}"
                sh 'sleep 2'
            }
        }
    }

    post {
        success {
            echo '✅ Pipeline finished successfully!'
        }
        failure {
            echo '❌ Pipeline failed. Check logs!'
        }
        always {
            echo 'Pipeline completed (success or failure).'
        }
    }
}
