pipeline {
    agent any
    stages {

        // CI Start
        stage('Build') {
            steps {
                echo 'Build'
                sh 'mvn package'
            }
        }

        stage('Fetch') {
            steps {
                // Setting up Git credentials
                withCredentials([string(credentialsId: 'a8258d45-efae-4d28-86bf-79340a5e8a00', variable: 'GIT_CREDENTIALS')]) {
                    sh "git config --global credential.helper 'store --file ~/.git-credentials'"
                }

                // Fetching changes from the remote Git repository
                sh "git rev-parse --resolve-git-dir /var/lib/jenkins/workspace/sonar_analysis_main@2/.git"
                sh "git config remote.origin.url https://github.com/nishant243/Jenkins.git"
                sh "git fetch --no-tags --force --progress -- https://github.com/nishant243/Jenkins.git +refs/heads/main:refs/remotes/origin/main"
            }
        }

        stage("SonarQube analysis") {
            agent any
            when {
                anyOf {
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
            }
        }
        // Ci Ended

        // CD Started
        stage('Deploy to Dev') {
            steps {
                echo 'Build'
            }
        }

        stage('Deploy to test ') {
            when {
                branch 'main'
            }
            steps {
                echo 'Build'
            }
        }
        // CD Ended
    }
}
