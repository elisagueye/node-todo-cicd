
pipeline {
    agent any

    environment {
        APP_PATH = 'C:\\Users\\BCS\\node-todo-cicd'
        SEMGREP_PATH = 'C:\\Users\\BCS\\AppData\\Local\\Programs\\Python\\Python314\\Scripts\\semgrep.exe'
        REPORT_EMAIL = 'ton.email@gmail.com'
    }

    stages {

        stage('1 - Checkout') {
            steps {
                echo 'Récupération du code depuis GitHub...'
                checkout scm
            }
        }

        stage('2 - Installation des dépendances') {
            steps {
                bat 'cd %APP_PATH% && npm install'
            }
        }

        stage('3 - Analyse SCA - npm audit') {
            steps {
                bat 'cd %APP_PATH% && npm audit > sca-report.txt 2>&1 || true'
                echo 'SCA terminé ✅'
            }
        }

        stage('4 - Analyse SAST - Semgrep') {
            steps {
                bat 'cd %APP_PATH% && "%SEMGREP_PATH%" --config auto . --output sast-report.txt 2>&1 || true'
                echo 'SAST Semgrep terminé ✅'
            }
        }
    }

    post {
        always {
            emailext(
                subject: "Rapport Sécurité - ${env.JOB_NAME} - Build #${env.BUILD_NUMBER} - ${currentBuild.result}",
                body: """
                    <h2>Rapport de Sécurité Jenkins</h2>
                    <table border='1'>
                        <tr><td><b>Projet</b></td><td>${env.JOB_NAME}</td></tr>
                        <tr><td><b>Build</b></td><td>#${env.BUILD_NUMBER}</td></tr>
                        <tr><td><b>Statut</b></td><td>${currentBuild.result}</td></tr>
                        <tr><td><b>Durée</b></td><td>${currentBuild.durationString}</td></tr>
                        <tr><td><b>Logs</b></td><td><a href='${env.BUILD_URL}'>${env.BUILD_URL}</a></td></tr>
                    </table>
                    <hr/>
                    <h3>Analyses effectuées :</h3>
                    <ul>
                        <li>SCA : npm audit → sca-report.txt</li>
                        <li>SAST : Semgrep → sast-report.txt</li>
                    </ul>
                """,
                mimeType: 'text/html',
                to: "${env.REPORT_EMAIL}",
                attachmentsPattern: '**/sca-report.txt, **/sast-report.txt'
            )
        }
        success {
            echo 'Pipeline terminé avec succès !'
        }
        failure {
            echo 'Pipeline échoué, vérifier les logs.'
        }
    }
}