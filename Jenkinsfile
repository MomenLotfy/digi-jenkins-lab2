pipeline {
    agent any
    
    triggers {
        githubPush()
    }
    
    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/MomenLotfy/digi-jenkins-lab2.git', branch: 'main'
            }
        }

        stage('Run PHPUnit Tests') {
            steps {
                sh '''
                    echo "PHPUnit version:"
                    phpunit --version
                    echo ""
                    echo "Running tests..."
                    phpunit tests/
                '''
            }
        }
    }

    post {
        success {
            echo '✅ All tests passed!'
        }
        failure {
            echo '❌ Tests failed — build marked as FAILED.'
        }
    }
}
