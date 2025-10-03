pipeline {
    agent any

    environment {
        GIT_REPO = 'git@github.com:MariemSaiidii/MyPortfolio-deployments.git'
        GIT_BRANCH = 'main'
        SSH_KEY = 'C:\\Users\\marie\\.ssh\\id_ed25519'
    }

    stages {

        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }

        stage('Checkout Repo') {
            steps {
                // Checkout using SSH key directly
                bat """
                    set GIT_SSH_COMMAND=ssh -i ${SSH_KEY} -o StrictHostKeyChecking=no
                    git clone -b ${GIT_BRANCH} ${GIT_REPO} .
                """
            }
        }

        stage('Update Helm Values') {
            steps {
                echo 'Updating backend-chart/values.yaml'
                powershell "(Get-Content backend-chart/values.yaml) -replace 'tag: .*', 'tag: \"latestjkl\"' | Set-Content backend-chart/values.yaml"
                
                echo 'Updating frontend-chart/values.yaml'
                powershell "(Get-Content frontend-chart/values.yaml) -replace 'tag: .*', 'tag: \"latestjkl\"' | Set-Content frontend-chart/values.yaml"
            }
        }

        stage('Commit & Push Changes') {
            steps {
                script {
                    bat """
                        git config user.name "mariem"
                        git config user.email "saidi.mariem@esprit.tn"
                        git add .
                        git commit -m "Update image tags"
                        set GIT_SSH_COMMAND=ssh -i ${SSH_KEY} -o StrictHostKeyChecking=no
                        git push origin ${GIT_BRANCH}
                    """
                }
            }
        }
    }

    post {
        success {
            echo "CD pipeline completed successfully!"
        }
        failure {
            echo "CD pipeline failed. Check SSH access, branch, or file paths."
        }
    }
}
