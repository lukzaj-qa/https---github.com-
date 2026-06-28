pipeline {
    agent any

    // ============================================
    // KROK 1: Konfiguracja narzędzi Jenkins
    // ============================================
    tools {
        // Upewnij się, że ta nazwa odpowiada konfiguracji NodeJS w Manage Jenkins -> Tools
        // To automatycznie ustawia PATH do Node.js i npm
        nodejs 'NodeJS_LTS' 
    }

    stages {
        // ============================================
        // KROK 2: Pobranie kodu z repozytorium Git
        // ============================================
        stage('Checkout Code') {
            steps {
                echo '📥 Pobieranie kodu z repozytorium Git...'
                // Klonuje repozytorium do workspace Jenkinsa
                checkout scm
                echo '✅ Kod pobrany pomyślnie'
            }
        }

        // ============================================
        // KROK 3: Przygotowanie środowiska Node.js
        // ============================================
        stage('Setup Node.js & Install Bruno CLI') {
            steps {
                echo '🔧 Weryfikacja wersji Node.js i npm...'
                // Wyświetl wersję Node.js (powinno być v18+)
                bat 'node -v'
                // Wyświetl wersję npm (powinno być 9+)
                bat 'npm -v'
                
                echo '📦 Instalacja Bruno CLI (narzędzie do testowania API)...'
                // Instaluje Bruno CLI globalnie (-g) w systemie
                bat 'call npm install -g @usebruno/cli'
                
                echo '🔍 Sprawdzenie zainstalowanej wersji Bruno...'
                bat 'bru --version'
                
                echo '✅ Środowisko Node.js gotowe'
            }
        }

        // ============================================
        // KROK 4: Uruchomienie testów API
        // ============================================
        stage('Run API Tests') {
            steps {
                echo '🧪 Wykonywanie testów API Bruno...'
                bat 'call bru run --env trello_v2 --reporter-json results.json --reporter-junit results.xml --reporter-html results.html'
            }
        }
        
        // KROK 5 zostaje całkowicie USUNIĘTY ze stages
    }

    // ============================================
    // KROK 6: Post-pipeline akcje
    // ============================================
    post {
        always {
            echo '🏁 Pipeline zakończony. Publikacja raportów...'
            
            // Raporty publikują się TUTAJ, więc uruchomią się ZAWSZE
            archiveArtifacts artifacts: 'results.json, results.xml, results.html', fingerprint: true, allowEmptyArchive: true
            
            junit allowEmptyResults: true, testResults: 'results.xml'
            
            publishHTML(target: [
                allowMissing: true,
                alwaysLinkToLastBuild: true,
                keepAll: true,
                reportDir: '.',
                reportFiles: 'results.html',
                reportName: 'Bruno API Test Report'
            ])
        }
        
        success {
            echo '✅ 🎉 SUKCES! Wszystkie testy API przeszły pomyślnie!'
        }
        
        failure {
            echo '❌ PORAŻKA! Niektóre testy API failnęły.'
        }
    }
}