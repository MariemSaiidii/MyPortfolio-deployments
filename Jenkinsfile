pipeline {
    agent any

    environment {
        GIT_USER = "mariem"
        GIT_EMAIL = "mariem.saiidi@esprit.tn"
    }

    stages {
        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }

        stage('Checkout Repo') {
            steps {
                // Checkout using HTTPS + PAT stored in Jenkins credential
                withCredentials([usernamePassword(credentialsId: 'githubtokenn', usernameVariable: 'GITHUB_USER', passwordVariable: 'GITHUB_TOKEN')]) {
                    checkout([
                        $class: 'GitSCM',
                        branches: [[name: 'main']],
                        userRemoteConfigs: [[
                            url: "https://%GITHUB_USER%:%GITHUB_TOKEN%@github.com/MariemSaiidii/MyPortfolio-deployments.git",
                            credentialsId: 'githubtokenn'
                        ]]
                    ])
                }
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
                    // Configure Git user
                    bat "git config --global user.name \"${GIT_USER}\""
                    bat "git config --global user.email \"${GIT_EMAIL}\""

                    // Add & commit changes
                    bat "git add ."
                    bat "git commit -m \"Update Helm image tags\" || echo No changes to commit"

                    // Push using Jenkins HTTPS credential
                    withCredentials([usernamePassword(credentialsId: 'githubtokenn', usernameVariable: 'GITHUB_USER', passwordVariable: 'GITHUB_TOKEN')]) {
                        bat "git push https://%GITHUB_USER%:%GITHUB_TOKEN%@github.com/MariemSaiidii/MyPortfolio-deployments.git main"
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
            echo "CD pipeline failed. Check token permissions or file paths."
        }
    }
}
