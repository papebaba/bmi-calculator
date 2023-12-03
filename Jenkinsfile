pipeline {
    agent any

    stages {
        stage('SCA: SonarQube Analysis') {
            steps {
                script {
                    // Ensuring SonarQube scanner is available
                    withSonarQubeEnv('test-sonar-scanner') {
                        sh 'sonar-scanner -Dsonar.projectKey=bmi-calculator -Dsonar.sources=src'
                    }
                }
            }
        }

        stage('Build') {
            steps {
                script {
                    // Building the Node.js and React app
                    sh 'npm install'
                    sh 'npm run build'
                }
            }
        }
    }

    post {
        always {
            // This is where you can configure the pipeline to wait for the SonarQube quality gate result
            script {
                timeout(time: 1, unit: 'HOURS') {
                    def qg = waitForQualityGate() // Requires the SonarQube webhook to be configured
                    if (qg.status != 'OK') {
                        error "Pipeline aborted due to quality gate failure: ${qg.status}"
                    }
                }
            }
        }
    }
}
