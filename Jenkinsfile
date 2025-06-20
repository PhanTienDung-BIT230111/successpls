pipeline {
    agent any

    stages {
        stage('Clone') {
            steps {
                echo 'Cloning source code'
                git branch: 'main', url: 'https://github.com/PhanTienDung-BIT230111/successpls.git'
            }
        }

        stage('Restore Packages') {
            steps {
                echo 'Restoring packages'
                bat 'dotnet restore'
            }
        }

        stage('Build') {
            steps {
                echo 'Building project'
                bat 'dotnet build --configuration Release'
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests'
                bat 'dotnet test --no-build --verbosity normal'
            }
        }

        stage('Publish to Folder') {
            steps {
                echo 'Publishing project'
                bat 'dotnet publish -c Release -o "%WORKSPACE%\\publish"'
            }
        }

        stage('Stop IIS') {
            steps {
                echo 'Stopping IIS to avoid sharing violation'
                bat 'iisreset /stop'
            }
        }

        stage('Copy to IIS folder') {
            steps {
                echo 'Copying files to IIS directory'
                bat '''
                robocopy "%WORKSPACE%\\publish" "C:\\wwwroot\\myproject" /MIR /R:5 /W:5
                '''
            }
        }

        stage('Start IIS') {
            steps {
                echo 'Starting IIS'
                bat 'iisreset /start'
            }
        }

        stage('Deploy to IIS') {
            steps {
                powershell '''
                Import-Module WebAdministration

                if (-not (Test-Path IIS:\\Sites\\MySite)) {
                    New-Website -Name "MySite" -Port 82 -PhysicalPath "C:\\wwwroot\\myproject" -ApplicationPool ".NET v4.5"
                } else {
                    Write-Host "Website already exists."
                }
                '''
            }
        }
    }
}
