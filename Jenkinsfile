pipeline {
    agent any

    environment {
        // Path to your SSH key
        GIT_SSH_KEY = "C:\\Users\\marie\\.ssh\\id_ed25519"
        GIT_USER = "mariem"
        GIT_EMAIL = "saidi.mariem@esprit.tn"
    }

    stages {
        stage('Checkout HTTPS') {
            steps {
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: 'main']],
                    userRemoteConfigs: [[
                        url: 'https://github.com/MariemSaiidii/MyPortfolio-deployments.git',
                        credentialsId: 'githubtokenn'
                    ]]
                ])
            }
        }

        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }

        stage('Checkout SSH Repo') {
            steps {
                // Use the SSH key
                bat """
                    set GIT_SSH_COMMAND=ssh -i ${GIT_SSH_KEY} -o StrictHostKeyChecking=no
                    git clone -b main git@github.com:MariemSaiidii/MyPortfolio-deployments.git .
                """
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
                    // Set Git config
                    bat "git config --global user.name \"${GIT_USER}\""
                    bat "git config --global user.email \"${GIT_EMAIL}\""

                    // Add, commit, and push
                    bat "git add ."
                    bat "git commit -m \"Update Helm image tags\" || echo No changes to commit"
                    bat """
                        set GIT_SSH_COMMAND=ssh -i ${GIT_SSH_KEY} -o StrictHostKeyChecking=no
                        git push origin main
                    """
                }
            }
        }
    }

    post {
        failure {
            echo "CD pipeline failed. Check SSH access, branch, or file paths."
        }
        success {
            echo "CD pipeline completed successfully!"
        }
    }
}
