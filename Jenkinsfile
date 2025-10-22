pipeline {
    agent any

    environment {
        GIT_REPO = 'https://github.com/MariemSaiidii/MyPortfolio-deployments.git'
        GIT_BRANCH = 'main'
        GITHUB_USER = 'MariemSaiidii'
        // Jenkins Secret Text credential containing your token
        GITHUB_TOKEN_ID = 'Token'
    }

    stages {

        stage('Checkout SCM') {
            steps {
                // Checkout main branch using the token
                checkout([$class: 'GitSCM',
                    branches: [[name: "refs/heads/${env.GIT_BRANCH}"]],
                    doGenerateSubmoduleConfigurations: false,
                    extensions: [[$class: 'CloneOption', shallow: false]],
                    userRemoteConfigs: [[
                        url: env.GIT_REPO,
                        credentialsId: env.GITHUB_TOKEN_ID
                    ]]
                ])
            }
        }

        stage('Update Helm Values') {
            steps {
                bat '''
                powershell -Command "(Get-Content backend-chart/values.yaml) -replace 'tag: .*', 'tag: latestjkl' | Set-Content backend-chart/values.yaml"
                powershell -Command "(Get-Content frontend-chart/values.yaml) -replace 'tag: .*', 'tag: latestjkl' | Set-Content frontend-chart/values.yaml"
                '''
            }
        }

        stage('Commit Changes') {
            steps {
                bat 'git config user.name "MariemSaiidii"'
                bat 'git config user.email "mariem.saiidi@gmail.com"'
                bat 'git add .'
                bat 'git commit -m "Update Helm image tags" || echo No changes to commit'
            }
        }

        stage('Push Changes') {
            steps {
                // Use Jenkins secret token for push
                withCredentials([string(credentialsId: env.GITHUB_TOKEN_ID, variable: 'GITHUB_TOKEN')]) {
                    bat 'git push https://%GITHUB_USER%:%GITHUB_TOKEN%@github.com/MariemSaiidii/MyPortfolio-deployments.git main'
                }
            }
        }
    }

    post {
        success {
            echo 'CD pipeline succeeded!'
        }
        failure {
            echo 'CD pipeline failed. Check token permissions, branch, or file paths.'
        }
    }
}
