pipeline {
    agent any

    environment {
        // Use SSH key with forward slashes
        GIT_SSH_COMMAND = 'ssh -i C:/Users/marie/.ssh/id_ed25519 -o StrictHostKeyChecking=no'
    }

    stages {

        stage('Checkout Repo') {
            steps {
                // Clean before clone if necessary
                deleteDir()
                // Clone via SSH (so push will work)
                bat 'git clone -b main git@github.com:MariemSaiidii/MyPortfolio-deployments.git .'
            }
        }

        stage('Update Helm Values') {
            steps {
                script {
                    if (fileExists('backend-chart/values.yaml')) {
                        echo 'Updating image tag in backend-chart/values.yaml'
                        bat 'powershell -Command "(Get-Content backend-chart/values.yaml) -replace \'tag: .*\', \'tag: \\"latestjkl\\"\ | Set-Content backend-chart/values.yaml"'
                    }

                    if (fileExists('frontend-chart/values.yaml')) {
                        echo 'Updating image tag in frontend-chart/values.yaml'
                        bat 'powershell -Command "(Get-Content frontend-chart/values.yaml) -replace \'tag: .*\', \'tag: \\"latestjkl\\"\ | Set-Content frontend-chart/values.yaml"'
                    }
                }
            }
        }

        stage('Commit & Push Changes') {
            steps {
                script {
                    bat 'git config user.name "mariem"'
                    bat 'git config user.email "saidi.mariem@esprit.tn"'

                    bat 'git add .'
                    bat 'git commit -m "Update image tags via Jenkins pipeline"'
                    bat 'git push origin main'
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'CD pipeline failed. Check SSH access, branch, or file paths.'
        }
    }
}
