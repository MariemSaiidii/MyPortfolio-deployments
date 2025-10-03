pipeline {
    agent any

    environment {
        // Use forward slashes for Windows Git SSH key path
        GIT_SSH_COMMAND = 'ssh -i C:/Users/marie/.ssh/id_ed25519 -o StrictHostKeyChecking=no'
    }

    stages {

        stage('Declarative: Checkout SCM') {
            steps {
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: 'main']],
                    doGenerateSubmoduleConfigurations: false,
                    extensions: [],
                    userRemoteConfigs: [[
                        url: 'git@github.com:MariemSaiidii/MyPortfolio-deployments.git',
                        credentialsId: 'github-ssh' // Jenkins credential ID for your private key
                    ]]
                ])
            }
        }

        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }

        stage('Update Helm Values') {
            steps {
                script {
                    if (fileExists('backend-chart/values.yaml')) {
                        echo "Updating image tag in backend-chart/values.yaml"
                        bat 'powershell -Command "(Get-Content backend-chart/values.yaml) -replace \\"tag: .*\\", \\"tag: \\"latestjkl\\"\\" | Set-Content backend-chart/values.yaml"'
                    }
                    if (fileExists('frontend-chart/values.yaml')) {
                        echo "Updating image tag in frontend-chart/values.yaml"
                        bat 'powershell -Command "(Get-Content frontend-chart/values.yaml) -replace \\"tag: .*\\", \\"tag: \\"latestjkl\\"\\" | Set-Content frontend-chart/values.yaml"'
                    }
                }
            }
        }

        stage('Commit & Push Changes') {
            steps {
                script {
                    // Configure Git user
                    bat 'git config --global user.name "mariem"'
                    bat 'git config --global user.email "saidi.mariem@esprit.tn"'

                    // Add changes
                    bat 'git add .'

                    // Commit changes
                    bat 'git commit -m "Update Helm chart tags via Jenkins" || echo "No changes to commit"'

                    // Push changes
                    bat 'git push origin main'
                }
            }
        }
    }

    post {
        failure {
            echo 'CD pipeline failed. Check SSH access, branch, or file paths.'
        }
        success {
            echo 'CD pipeline completed successfully!'
        }
    }
}
