
pipeline {
    agent any

    options {
        skipDefaultCheckout(true)
        timestamps()
    }

    stages {

        // 1. Checkout GitHub repository
        stage('Checkout') {
            steps {
                echo 'Checking out GitHub repository'

                checkout scm

                bat 'git branch'
                bat 'git log -1 --oneline'
            }
        }

        // 2. Verify required website files
        stage('Verify Files') {
            steps {
                echo 'Verifying website files'

                bat '''
                if not exist index.html exit /b 1
                if not exist style.css exit /b 1
                if not exist README.md exit /b 1
                '''

                echo 'All required files exist'
            }
        }

        // 3. Automated website testing
        stage('Run Tests') {
            steps {
                echo 'Running website test cases'

                // TC01: index.html exists and is not empty
                bat '''
                for %%A in (index.html) do if %%~zA EQU 0 exit /b 1
                '''

                // TC02: style.css exists and is not empty
                bat '''
                for %%A in (style.css) do if %%~zA EQU 0 exit /b 1
                '''

                // TC03: HTML contains stylesheet reference
                bat '''
                findstr /I /C:"style.css" index.html >nul
                if errorlevel 1 exit /b 1
                '''

                // TC04: HTML contains HTML document tag
                bat '''
                findstr /I /C:"<html" index.html >nul
                if errorlevel 1 exit /b 1
                '''

                // TC05: HTML contains body tag
                bat '''
                findstr /I /C:"<body" index.html >nul
                if errorlevel 1 exit /b 1
                '''

                echo All website test cases passed!
            }
        }

        // 4. Deploy website to GitHub Pages
        stage('Deploy to GitHub Pages') {
            steps {
                echo 'Deploying website to gh-pages branch'

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

                    git push https://x-access-token:%GIT_TOKEN%@github.com/vyshnavi-cirrus/-cicd.git HEAD:gh-pages --force

                    if errorlevel 1 exit /b 1
                    '''

                }
            }
        }
    }

    post {
        success {
            echo 'SUCCESS: Jenkins pipeline completed!'
            echo 'Website deployment successful!'
            echo 'GitHub Pages: https://vyshnavi-cirrus.github.io/-cicd/'
        }

        failure {
            echo 'FAILURE: Jenkins pipeline failed'
            echo 'Check Console Output for details'
        }

        always {
            echo 'Pipeline execution completed'
        }
    }
}
