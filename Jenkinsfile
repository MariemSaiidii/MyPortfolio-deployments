pipeline {
    agent any

    environment {
        GIT_USER = "MariemSaiidii"
        GIT_EMAIL = "mariem.saiidi@gmail.com"
        GITHUB_TOKEN = credentials('githubtoken') // Jenkins secret text with your PAT
        REPO_URL = "https://github.com/MariemSaiidii/MyPortfolio-deployments.git"
    }

    stages {
        stage('Checkout Repo') {
            steps {
                // Checkout using HTTPS + PAT
                checkout([$class: 'GitSCM',
                    branches: [[name: 'main']],
                    userRemoteConfigs: [[
                        url: "${REPO_URL}",
                        credentialsId: 'githubtoken'
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
                    // Update backend-chart
                    bat '''
                    powershell -Command "(Get-Content backend-chart/values.yaml) -replace 'tag: .*', 'tag: latestjkl' | Set-Content backend-chart/values.yaml"
                    '''
                    // Update frontend-chart
                    bat '''
                    powershell -Command "(Get-Content frontend-chart/values.yaml) -replace 'tag: .*', 'tag: latestjkl' | Set-Content frontend-chart/values.yaml"
                    '''
                }
            }
        }

        stage('Commit & Push Changes') {
            steps {
                script {
                    // Configure Git
                    bat "git config --global user.name \"${GIT_USER}\""
                    bat "git config --global user.email \"${GIT_EMAIL}\""

                    // Commit changes
                    bat "git add ."
                    bat "git commit -m \"Update Helm image tags\" || echo No changes to commit"

                    // Push using HTTPS + PAT
                    bat """
                        git push https://${GIT_USER}:${GITHUB_TOKEN}@github.com/MariemSaiidii/MyPortfolio-deployments.git main
                    """
                }
            }
        }
    }

    post {
        failure {
            echo "CD pipeline failed. Check token permissions or file paths."
        }
        success {
            echo "CD pipeline completed successfully!"
        }
    }
}
