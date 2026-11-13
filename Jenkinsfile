
pipeline {
    agent any

    options {
        skipDefaultCheckout(true)
        timestamps()
    }

    stages {

        // 1. Checkout
        stage('Checkout') {
            steps {
                echo 'Getting source code from GitHub'
                checkout scm
            }
        }

        // 2. Verify Files
        stage('Verify Files') {
            steps {
                echo 'Checking project files'

                bat 'if not exist index.html exit /b 1'
                bat 'if not exist style.css exit /b 1'

                echo 'All required files are present'
            }
        }

        // 3. Run Tests
        stage('Run Tests') {
            steps {
                echo 'Running website test cases'

                // TC01: HTML file is not empty
                bat 'for %%A in (index.html) do if %%~zA EQU 0 exit /b 1'

                // TC02: CSS file is not empty
                bat 'for %%A in (style.css) do if %%~zA EQU 0 exit /b 1'

                // TC03: HTML references CSS
                bat 'findstr /I /C:"style.css" index.html >nul'

                // TC04: HTML contains HTML tag
                bat 'findstr /I /C:"<html" index.html >nul'

                echo 'All test cases passed'
            }
        }

        // 4. Deploy to GitHub Pages
        stage('Deploy to GitHub Pages') {
            steps {
                echo 'Deploying to gh-pages'

                withCredentials([
                    string(
                        credentialsId: 'github-pat',
                        variable: 'GIT_TOKEN'
                    )
                ]) {
                    bat '''
                    @echo off
                    git config user.name "Jenkins"
                    git config user.email "jenkins@example.com"

                    git push https://x-access-token:%GIT_TOKEN%@github.com/vyshnavi-cirrus/-cicd.git HEAD:refs/heads/gh-pages --force

                    if errorlevel 1 exit /b 1
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'SUCCESS: Website deployed successfully!'
            echo 'https://vyshnavi-cirrus.github.io/-cicd/'
        }

        failure {
            echo 'FAILURE: Check Jenkins Console Output'
        }

        always {
            echo 'Pipeline execution completed'
        }
    }
}
