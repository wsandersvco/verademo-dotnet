//
// This is an example of using VeraDemo Java test application with the Veracode Static scanner.
//

pipeline {
    agent {
        label 'linux && dotnet8 && amd64'
    }

    environment {
        VERACODE_APP_NAME = 'Jenkins Pipeline - verademo-dotnetcore'      // App Name in the Veracode Platform
    }

    stages {
        // this assumes you have the `dotnet` command-line tools installed and available to Jenkins
        stage('Build') {
            steps {
                sh 'dotnet publish -c Debug -p:UseAppHost=false app/app.csproj'
            }
            post {
                success {
                    sh 'zip -rv publish.zip ./app/bin/Debug/*/publish/'
                }
            }
        }
        stage('Veracode Upload and Scan') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'Veracode API Credentials',
                    passwordVariable: 'vkey', usernameVariable: 'vid')]) {
                    veracode applicationName: "${VERACODE_APP_NAME}", canFailJob: true, createProfile: true,
                        criticality: 'VeryHigh', deleteIncompleteScanLevel: '1', fileNamePattern: 'publish.zip',
                        replacementPattern: 'publish.zip', sandboxName: '', scanExcludesPattern: '',
                        scanIncludesPattern: '', scanName: '$buildnumber', teams: 'Default Team',
                        timeout: 60, uploadIncludesPattern: 'publish.zip',
                        vid: vid, vkey: vkey, waitForScan: true
                }
            }
        }
    }
}
