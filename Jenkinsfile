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
        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }

        stage('Checkout Repo') {
            steps {
                git branch: "${BRANCH}",
                    credentialsId: 'github-ssh', // Your Jenkins SSH credential ID
                    url: "${GIT_REPO}"
            }
        }

        stage('Update Helm Values') {
            steps {
                script {
                    def charts = ['backend-chart', 'frontend-chart']
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

        stage('Commit & Push Changes') {
            steps {
                script {
                    // Configure Git user
                    bat """
                        git config --global user.name "mariem"
                        git config --global user.email "saidi.mariem@esprit.tn"
                    """

                    // Use SSH Agent to push
                    sshagent(['github-ssh']) { // Your Jenkins SSH credential ID
                        bat """
                            git add backend-chart\\values.yaml frontend-chart\\values.yaml
                            git commit -m "Update Helm image tags to ${params.IMAGE_TAG}" || exit 0
                            git push origin ${BRANCH}
                        """
                    }
                }
            }
        }
    }

    post {
        success {
            echo 'CD pipeline executed successfully.'
        }
        failure {
            echo 'CD pipeline failed. Check SSH access, branch, or file paths.'
        }
    }
}
