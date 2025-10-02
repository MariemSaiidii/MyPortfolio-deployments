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
                            sh "sed -i 's|tag: .*|tag: \"${params.IMAGE_TAG}\"|' ${valuesFile}"
                        } else {
                            error("File not found: ${valuesFile}")
                        }
                    }
                }
            }
        }

        stage('Commit & Push Changes') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'github', usernameVariable: 'GIT_USERNAME', passwordVariable: 'GIT_PASSWORD')]) {
                    sh """
                        git config --global user.name "mariem"
                        git config --global user.email "saidi.mariem@esprit.tn"
                        git add backend-chart/values.yaml frontend-chart/values.yaml
                        git commit -m "🔄 Update Helm image tags to ${params.IMAGE_TAG}" || echo "No changes to commit"
                        git push https://${GIT_USERNAME}:${GIT_PASSWORD}@${GIT_REPO} ${BRANCH}
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
            echo 'CD pipeline failed.'
        }
    }
}