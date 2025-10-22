pipeline {
    agent any

    environment {
        GIT_USER = "MariemSaiidii"
        GIT_EMAIL = "mariem.saiidi@gmail.com"
    }

    stages {
        stage('Checkout Repository') {
            steps {
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: 'main']],
                    userRemoteConfigs: [[
                        url: 'https://github.com/MariemSaiidii/MyPortfolio-deployments.git',
                        credentialsId: 'githubtokenn'  // your GitHub HTTPS token for initial checkout
                    ]]
                ])
            }
        }

        stage('Update Helm Values') {
            steps {
                script {
                    bat '''
                        powershell -Command "(Get-Content backend-chart/values.yaml) -replace 'tag: .*', 'tag: latestjkl' | Set-Content backend-chart/values.yaml"
                        powershell -Command "(Get-Content frontend-chart/values.yaml) -replace 'tag: .*', 'tag: latestjkl' | Set-Content frontend-chart/values.yaml"
                    '''
                }
            }
        }

        stage('Commit & Push Changes') {
            steps {
                script {
                    // Make sure we are on main branch
                    bat "git checkout main"

                    // Set Git user info
                    bat "git config --global user.name \"${GIT_USER}\""
                    bat "git config --global user.email \"${GIT_EMAIL}\""

                    // Add & commit changes
                    bat "git add ."
                    bat "git commit -m \"Update Helm image tags\" || echo No changes to commit"

                    // Use the Token credential for HTTPS push
                    withCredentials([string(credentialsId: 'Token', variable: 'GITHUB_TOKEN')]) {
                        bat "git push https://MariemSaiidii:%GITHUB_TOKEN%@github.com/MariemSaiidii/MyPortfolio-deployments.git main"
                    }
                }
            }
        }
    }

    post {
        success {
            echo "CD pipeline completed successfully!"
        }
        failure {
            echo "CD pipeline failed. Check token permissions, branch, or file paths."
        }
    }
}
