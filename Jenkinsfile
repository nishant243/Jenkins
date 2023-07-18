pipeline {
    agent any

    environment {
        function_name = 'Jenkinsjava'
    }

    stages {

        // CI Start
        stage('Build') {
            steps {
                echo 'Build'
                sh 'mvn package'
            }
        }
    stages {
        stage('Checkout') {
            steps {
                script {
                    def gitUrl = 'https://github.com/nishant243/Jenkins.git'
                    def commitHash = 'f944253d2758129acbbc59db6c2697b8ce07543e'
                    def repoDir = '/var/lib/jenkins/workspace/sonar_analysis_main@2'

                    // Clone the repository
                    git credentialsId: 'a8258d45-efae-4d28-86bf-79340a5e8a00',
                        url: gitUrl,
                        branch: 'main',
                        skipTag: true

                    // Checkout the specific commit
                    dir(repoDir) {
                        sh "git checkout -f ${commitHash}"
                    }
                }
            }

        stage("SonarQube analysis") {
            agent any

            when {
                anyOf {
                    branch 'feature/*'
                    branch 'main'
                }
            }
            steps {
                withSonarQubeEnv('Sonar') {
                    sh 'mvn sonar:sonar'
                }
            }
        }
        stage("Quality Gate") {
            steps {
                script {
                    try {
                        timeout(time: 10, unit: 'MINUTES') {
                            waitForQualityGate abortPipeline: true
                        }
                    }
                    catch (Exception ex) {

                    }
                }
            }
        }

        stage('Push') {
            steps {
                echo 'Push'

               // sh "aws s3 cp target/sample-1.0.3.jar s3://bermtecbatch31"
            }
        }

        // Ci Ended

        // CD Started

        stage('Deployments') {
            parallel {

                stage('Deploy to Dev') {
                    steps {
                        echo 'Build'

                        // sh "aws lambda update-function-code --function-name $function_name --region us-east-1 --s3-bucket bermtecbatch31 --s3-key sample-1.0.3.jar"
                    }
                }

                stage('Deploy to test ') {
                    when {
                        branch 'main'
                    }
                    steps {
                        echo 'Build'

                        // sh "aws lambda update-function-code --function-name $function_name --region us-east-1 --s3-bucket bermtecbatch31 --s3-key sample-1.0.3.jar"
                    }
                }
            }
        }


        

        // CD Ended
    }
}
