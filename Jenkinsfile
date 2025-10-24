pipeline {
    agent any

    environment {
        GIT_REPO = 'https://github.com/MariemSaiidii/MyPortfolio-deployments.git'
        BRANCH = 'main'
        IMAGE_TAG = 'latestjkl'
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: "${BRANCH}", credentialsId: 'githubtokenn', url: "${GIT_REPO}"
            }
        }

        stage('Update Helm Values') {
            steps {
                script {
                    def files = ['backend-chart/values.yaml', 'frontend-chart/values.yaml']
                    files.each { file ->
                        if (fileExists(file)) {
                            echo "Updating image tag in ${file}"
                            powershell "(Get-Content ${file}) -replace 'tag: .*', 'tag: ${IMAGE_TAG}' | Set-Content ${file}"
                        } else {
                            error("File not found: ${file}")
                        }
                    }
                }
            }
        }

        stage('Commit & Push Changes') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'GitToken', usernameVariable: 'GIT_USERNAME', passwordVariable: 'GIT_PASSWORD')]) {
                    bat """
                        git config user.name "MariemSaiidii"
                        git config user.email "mariem.saiidi@gmail.com"
                        git add backend-chart/values.yaml frontend-chart/values.yaml
                        git commit -m "🔄 Update Helm image tags to ${IMAGE_TAG}" || echo No changes to commit
                        git push https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/MariemSaiidii/MyPortfolio-deployments.git ${BRANCH}
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
            echo 'CD pipeline failed. Check token permissions, branch, or file paths.'
        }
    }
}
