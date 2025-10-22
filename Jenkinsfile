pipeline {
    agent any

    environment {
        GIT_USER = "mariem"
        GIT_EMAIL = "mariem.saiidi@gmail.com"
    }

    stages {
        stage('Checkout Repo') {
            steps {
                // Checkout using Jenkins token
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: 'main']],
                    userRemoteConfigs: [[
                        url: 'https://github.com/MariemSaiidii/MyPortfolio-deployments.git',
                        credentialsId: 'Token'
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

                    // Add, commit
                    bat "git add ."
                    bat "git commit -m \"Update Helm image tags\" || echo No changes to commit"

                    // Push using token
                    withCredentials([string(credentialsId: 'Token', variable: 'GITHUB_TOKEN')]) {
                        bat "git push https://mariem:${GITHUB_TOKEN}@github.com/MariemSaiidii/MyPortfolio-deployments.git main"
                    }
                }
            }
        }
    }

    post {
        failure {
            echo "CD pipeline failed. Check token permissions or file paths."
        }
        success {
            echo "CD pipeline completed successfully!"
        }
    }
}
