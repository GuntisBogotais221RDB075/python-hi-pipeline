pipeline {
    agent any
    stages {
        stage('install-pip-deps') {
            steps {
                script {
                    echo "Sākam instalēt pip atkarības un sagatavot vidi"
                    // Klonējam python repozitoriju
                    bat 'git clone https://github.com/mtararujs/python-greetings.git'
                    echo "Klonēšana pabeigta, failu saturs direktorijā python-greetings:"
                    bat 'dir python-greetings'
                    // Instalējam nepieciešamos Python bibliotēkas (izmantojot pip)
                    bat 'pip install -r python-greetings\\requirements.txt'
                }
            }
        }
        stage('deploy-to-dev') {
            steps {
                script {
                    deployApplication('dev', '7001')
                }
            }
        }
        stage('tests-on-dev') {
            steps {
                script {
                    testApplication('dev')
                }
            }
        }
        // Pārliecinieties, ka visiem nākamajiem posmiem tiek izmantots arī "bat" izsaukums...
    }
    post {
        always {
            echo "Piegādes konvejera process ir pabeigts."
        }
    }
}

// Funkcija izvietošanas posmiem
def deployApplication(environment, port) {
    echo "Sāku izvietošanu vidē: ${environment}"
    // Klonējam python repozitoriju, kas satur lietojumprogrammas kodu
    bat "git clone https://github.com/mtararujs/python-greetings.git"
    echo "Pārbaudām un apstādinām, ja nepieciešams, iepriekšējo instanci vidē ${environment}"
    // pm2 komanda – Windows vidē var tikt izmantota bez '|| true'
    bat "pm2 delete greetings-app-${environment}"
    echo "Sākam servisa startēšanu vidē ${environment} uz porta ${port}"
    // Startējam aplikāciju ar norādīto portu; pielāgojiet ceļu uz app.py, ja nepieciešams
    bat "pm2 start python-greetings\\app.py --name greetings-app-${environment} -- --port ${port}"
}

// Funkcija testēšanas posmiem
def testApplication(environment) {
    echo "Sāku testēšanu vidē: ${environment}"
    // Klonējam testu repozitoriju, kas satur API testu kodu
    bat "git clone https://github.com/mtararujs/course-js-api-framework.git"
    echo "Instalējam npm atkarības testu veikšanai"
    bat "npm install --prefix course-js-api-framework"
    echo "Sākam testa komandas izpildi: npm run greetings greetings_${environment}"
    bat "npm run greetings greetings_${environment} --prefix course-js-api-framework"
}
