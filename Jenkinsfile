pipeline {
    agent any

    stages {
        stage('Checkout Code from GitHub') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], userRemoteConfigs: [[url: 'https://github.com/HarryRichard08/Scrappy-template.git']]])
            }
        }

        stage('SonarQube Analysis') {
            environment {
                SCANNER_HOME = tool 'sonar-scanner'
            }
            steps {
                withSonarQubeEnv('sonar-server') {
                    sh "${SCANNER_HOME}/bin/sonar-scanner -Dsonar.projectName=test -Dsonar.projectKey=test"
                }
            }
        }

        // ... (other stages remain the same)

        stage('Copy File to Remote Server') {
            steps {
                unstash 'scrapyTemplateStash'

                script {
                    // ... (existing script remains the same)
                }
            }
        }
    }

    post {
        always {
            script {
                try {
                    def commitInfo = sh(script: "git show -s --format='%ae'", returnStdout: true).trim()
                    emailext(
                        subject: "Build Notification for Branch '${env.GIT_BRANCH}'",
                        body: """Hello,

This email is to notify you that a build has been performed on the branch '${env.GIT_BRANCH}' in the ${env.JOB_NAME} job.

Build Details:
- Build Number: ${env.BUILD_NUMBER}
- Build Status: ${currentBuild.currentResult}
- Commit ID: ${env.GIT_COMMIT}

Please review the build and attached changes.

Best regards,
The Jenkins Team
""",
                        to: commitInfo, // Send the email to the last committer
                        mimeType: 'text/plain'
                    )
                } catch (Exception e) {
                    echo "Failed to send email: ${e.getMessage()}"
                }
            }
        }
    }
}

def readFileFromGit(String filePath) {
    return sh(script: "git show origin/main:${filePath}", returnStdout: true).trim()
}
