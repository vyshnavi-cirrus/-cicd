
pipeline {
    agent any

    stages {

        // 1. Checkout source code
        stage('Checkout') {
            steps {
                echo 'Checking out source code from GitHub'

                checkout scm
            }
        }

        // 2. Verify project files
        stage('Verify Files') {
            steps {
                echo 'Checking required project files'

                bat '''
                if not exist index.html exit /b 1
                if not exist style.css exit /b 1
                if not exist README.md exit /b 1
                '''

                echo 'Required files verified successfully'
            }
        }

        // 3. Run website test cases
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

                // TC03: HTML references stylesheet
                bat '''
                findstr /I /C:"style.css" index.html >nul
                if errorlevel 1 exit /b 1
                '''

                // TC04: HTML contains html tag
                bat '''
                findstr /I /C:"<html" index.html >nul
                if errorlevel 1 exit /b 1
                '''

                echo All test cases passed successfully!
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
                    git config user.name "Jenkins"
                    git config user.email "jenkins@example.com"

                    git push https://x-access-token:%GIT_TOKEN%@github.com/vyshnavi-cirrus/-cicd.git HEAD:refs/heads/gh-pages --force
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'SUCCESS: Website deployed to GitHub Pages!'
            echo 'Repository: https://github.com/vyshnavi-cirrus/-cicd'
        }

        failure {
            echo 'FAILED: Check Jenkins Console Output'
        }

        always {
            echo 'Jenkins pipeline execution completed'
        }
    }
}
