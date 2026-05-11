pipeline {
    agent { label 'node-agent' }
    triggers {
        githubPush()
    }
    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/MomenLotfy/digi-jenkins-lab2.git', branch: 'master'
            }
        }

        

        stage('Run PHPUnit Tests') {
            steps {
                sh 'phpunit tests/OrderTest.php tests/SubscriptionTest.php'
            }
        }
    }

    post {
        success {
            echo 'All tests passed!'
        }
        failure {
            echo 'Tests failed — build marked as FAILED.'
        }
    }
}
