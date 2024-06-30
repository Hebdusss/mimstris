pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                script {
                    try {
                        // Pobranie najnowszej wersji kodu źródłowego
                        checkout scm
                        // Budowanie obrazu Docker
                        sh 'docker image build -t react-tetris:latest .'
                        // Powiadomienie o sukcesie
                        echo 'Build stage completed successfully'
                    } catch (Exception e) {
                        // Powiadomienie o porażce
                        echo 'Build stage failure'
                        error 'Build failed'
                    }
                }
            }
        }
        stage('Test') {
            steps {
                script {
                    try {
                        // Budowanie obrazu Docker do testów
                        sh 'docker image build -t react-tetris-tests -f DockerfileTest .'
                        // Uruchamianie testów
                        sh 'docker run react-tetris-tests > test-results.txt'
                        // Odczytanie wyników testów
                        def testResults = readFile('test-results.txt')
                        // Powiadomienie o wynikach
                        echo testResults
                        // Sprawdzanie, czy wszystkie testy się powiodły
                        if (testResults.contains('All tests passed')) {
                            echo 'All tests passed'
                        } else {
                            echo 'Some tests failed'
                            error 'Test stage failed'
                        }
                    } catch (Exception e) {
                        // Powiadomienie o porażce testów
                        echo 'Testing stage failure'
                        error 'Test failed'
                    }
                }
            }
        }
        stage('Deploy') {
            steps {
                script {
                    try {
                        // Uruchamianie kontenera z aplikacją
                        sh 'docker run -d -p 3000:3000 --name react-tetris react-tetris:latest'
                        // Powiadomienie o sukcesie
                        echo 'Deploy stage completed successfully'
                    } catch (Exception e) {
                        // Powiadomienie o porażce
                        echo 'Deploy stage failure'
                        error 'Deploy failed'
                    }
                }
            }
        }
    }

    post {
        always {
            // Powiadomienie o zakończeniu pipeline'a
            echo 'Pipeline completed'
        }
    }
}
