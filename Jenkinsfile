pipeline {
    agent any

    environment {
        GIT_USER = "mariem"
        GIT_EMAIL = "mariem.saiidi@gmail.com"
        REPO_URL = "https://github.com/MariemSaiidii/MyPortfolio-deployments.git"
    }

    stages {

        stage('Checkout Repo') {
            steps {
                cleanWs()
                withCredentials([string(credentialsId: 'githubtoken', variable: 'GITHUB_TOKEN')]) {
                    bat """
                        git config --global user.name "${GIT_USER}"
                        git config --global user.email "${GIT_EMAIL}"
                        git clone https://${GIT_USER}:${GITHUB_TOKEN}@github.com/MariemSaiidii/MyPortfolio-deployments.git .
                    """
                }
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
            bat '''
                git add .
                git commit -m "Update Helm image tags" || echo No changes to commit
                git push https://MariemSaiidii:%GITHUB_TOKEN%@github.com/MariemSaiidii/MyPortfolio-deployments.git main
            '''
        }
    }
}

    }

    post {
        failure {
            echo "❌ CD pipeline failed. Check token permissions or file paths."
        }
        success {
            echo "✅ CD pipeline completed successfully!"
        }
    }
}
