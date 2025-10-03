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
                    def charts = ['backend-chart', 'frontend-chart'] // Adjust if more charts exist

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
                withCredentials([string(credentialsId: 'github', variable: 'GIT_TOKEN')]) {
                    // Ensure the workspace is on the main branch
                    bat "git checkout ${BRANCH}"
                    // Stage and commit local changes first
                    bat """
                        git config --global user.name \"mariem\"
                        git config --global user.email \"saidi.mariem@esprit.tn\"
                        git add backend-chart\\values.yaml frontend-chart\\values.yaml
                        git commit -m \"🔄 Update Helm image tags to ${params.IMAGE_TAG}\" || exit 0
                    """
                    // Sync with remote and push
                    bat "git pull --rebase origin ${BRANCH}"
                    bat "git push origin ${BRANCH}"
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