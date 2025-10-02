pipeline {
    agent any
    environment {
        GIT_REPO = 'https://github.com/MariemSaiidii/MyPortfolio-deployments.git'
        BRANCH = 'main'
    }
    parameters {
        string(name: 'IMAGE_TAG', defaultValue: 'latest', description: 'Image tag from CI pipeline (e.g., 1.0.0-18)')
    }
    stages {
        stage('Update Helm Values') {
            steps {
                script {
                    def charts = ['backend-chart', 'frontend-chart'] // MySQL tag remains static

                    charts.each { chart ->
                        def valuesFile = "${chart}/values.yaml"
                        if (fileExists(valuesFile)) {
                            echo "Updating image tag in ${valuesFile}"
                            bat """
                                powershell -Command "(Get-Content ${valuesFile}) -replace 'tag: .*', 'tag: \"${params.IMAGE_TAG}\"' | Set-Content ${valuesFile}"
                            """
                        } else {
                            error("File not found: ${valuesFile}")
                        }
                    }
                }
            }
        }

        stage('Commit & Sync Changes') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'githubCD', usernameVariable: 'GIT_USERNAME', passwordVariable: 'GIT_PASSWORD')]) {
                    // Stage and commit local changes first
                    bat """
                        git config --global user.name "mariem"
                        git config --global user.email "saidi.mariem@esprit.tn"
                        git add backend-chart\\values.yaml frontend-chart\\values.yaml
                        git commit -m "🔄 Temporary commit: Update Helm image tags to ${params.IMAGE_TAG}" || exit 0
                    """
                    // Sync with remote and push using environment variables
                    bat "git pull https://%%GIT_USERNAME%%:%%GIT_PASSWORD%%@github.com/MariemSaiidii/MyPortfolio-deployments.git ${BRANCH} --rebase"
                    bat """
                        git push https://%%GIT_USERNAME%%:%%GIT_PASSWORD%%@github.com/MariemSaiidii/MyPortfolio-deployments.git ${BRANCH}
                    """
                }
            }
        }
    }
    post {
        success {
            echo 'CD pipeline executed successfully.'
        }
        failure {
            echo 'CD pipeline failed. Check GitHub token, repository permissions, or URL configuration.'
        }
    }
}