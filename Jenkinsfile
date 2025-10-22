pipeline {
    agent any

    environment {
        GIT_USER = "mariem"
        GIT_EMAIL = "mariiemsaiidi@gmail.com"
        GIT_REPO = "https://github.com/MariemSaiidii/MyPortfolio-deployments.git"
    }

    stages {
        stage('Checkout Repository') {
            steps {
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: 'main']],
                    userRemoteConfigs: [[
                        url: "${GIT_REPO}",
                        credentialsId: 'githubtoken'  // Jenkins secret text
                    ]]
                ])
            }
        }

        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }

        stage('Update Helm Values') {
            steps {
                script {
                    bat '''
                    powershell -Command "(Get-Content backend-chart/values.yaml) -replace 'tag: .*', 'tag: latestjkl' | Set-Content backend-chart/values.yaml"
                    powershell -Command "(Get-Content frontend-chart/values.yaml) -replace 'tag: .*', 'tag: latestjkl' | Set-Content frontend-chart/values.yaml"
                    '''
                }
            }
        }

        stage('Commit & Push Changes') {
            steps {
                withCredentials([string(credentialsId: 'githubtoken', variable: 'GITHUB_TOKEN')]) {
                    bat """
                        git config --global user.name "${GIT_USER}"
                        git config --global user.email "${GIT_EMAIL}"

                        git add .
                        git commit -m "Update Helm image tags" || echo No changes to commit
                        
                        git remote set-url origin https://${GIT_USER}:${GITHUB_TOKEN}@github.com/MariemSaiidii/MyPortfolio-deployments.git
                        git push origin main
                    """
                }
            }
        }
    }

    post {
        failure {
            echo "❌ CD pipeline failed. Check token permissions or file paths."
        }
        success {
            echo "✅ CD pipeline completed successfully using HTTPS + GitHub token!"
        }
    }
}
