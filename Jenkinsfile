pipeline {
    agent any

    environment {
        GIT_REPO = 'git@github.com:MariemSaiidii/MyPortfolio-deployments.git'
        BRANCH = 'main'
    }

    parameters {
        string(name: 'IMAGE_TAG', defaultValue: 'latest', description: 'Image tag from CI pipeline (e.g., 1.0.0-18)')
    }

    stages {
        stage('Update Helm Values') {
            steps {
                script {
                    def charts = ['backend-chart', 'frontend-chart'] // Add more charts if needed

                    charts.each { chart ->
                        def valuesFile = "${chart}/values.yaml"
                        if (fileExists(valuesFile)) {
                            echo "Updating image tag in ${valuesFile}"
                            bat """
                                powershell -Command "(Get-Content ${valuesFile}) -replace 'tag: .*', 'tag: \\"${params.IMAGE_TAG}\\"' | Set-Content ${valuesFile}"
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
                bat """
                    git config --global user.name "mariem"
                    git config --global user.email "saidi.mariem@esprit.tn"

                    git checkout ${BRANCH}
                    git add backend-chart\\values.yaml frontend-chart\\values.yaml
                    git commit -m "Update Helm image tags to ${params.IMAGE_TAG}" || exit 0

                    git push origin HEAD:${BRANCH}
                """
            }
        }
    }

    post {
        success {
            echo 'CD pipeline executed successfully.'
        }
        failure {
            echo 'CD pipeline failed. Make sure the Jenkins agent has SSH access to GitHub.'
        }
    }
}
