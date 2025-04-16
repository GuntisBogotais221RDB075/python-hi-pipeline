pipeline {
    agent any
    stages {
        stage('install-pip-deps') {
            steps {
                script {
                    echo "Sākam instalēt pip atkarības un sagatavot vidi"
                    // Klonējam python repozitoriju
                    sh 'git clone https://github.com/mtararujs/python-greetings.git'
                    echo "Klonēšana pabeigta, failu saturs direktorijā python-greetings:"
                    sh 'ls -la python-greetings'
                    // Instalējam nepieciešamos Python bibliotēkas (izmantojot pip3, ja nepieciešams)
                    sh 'pip3 install -r python-greetings/requirements.txt'
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
        stage('deploy-to-staging') {
            steps {
                script {
                    deployApplication('staging', '7002')
                }
            }
        }
        stage('tests-on-staging') {
            steps {
                script {
                    testApplication('staging')
                }
            }
        }
        stage('deploy-to-preprod') {
            steps {
                script {
                    deployApplication('preprod', '7003')
                }
            }
        }
        stage('tests-on-preprod') {
            steps {
                script {
                    testApplication('preprod')
                }
            }
        }
        stage('deploy-to-prod') {
            steps {
                script {
                    deployApplication('prod', '7004')
                }
            }
        }
        stage('tests-on-prod') {
            steps {
                script {
                    testApplication('prod')
                }
            }
        }
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
    sh "git clone https://github.com/mtararujs/python-greetings.git"
    echo "Pārbaudām un apstādam, ja nepieciešams, iepriekšējo instanci vidē ${environment}"
    // pm2 komanda – Unix sistēmā izmantojam "|| true", lai konveijers nepārrāvjas, ja serviss neeksistē
    sh "pm2 delete greetings-app-${environment} || true"
    echo "Sākam servisa startēšanu vidē ${environment} uz porta ${port}"
    // Startējam aplikāciju ar norādīto portu; pielāgojiet ceļu uz app.py, ja nepieciešams
    sh "pm2 start python-greetings/app.py --name greetings-app-${environment} -- --port ${port}"
}

// Funkcija testēšanas posmiem
def testApplication(environment) {
    echo "Sāku testēšanu vidē: ${environment}"
    // Klonējam testu repozitoriju, kas satur API testu kodu
    sh "git clone https://github.com/mtararujs/course-js-api-framework.git"
    echo "Instalējam npm atkarības testu veikšanai"
    // Izmantojam --prefix, lai npm darbotos attiecīgajā direktorijā
    sh "npm install --prefix course-js-api-framework"
    echo "Sākam testa komandas izpildi: npm run greetings greetings_${environment}"
    sh "npm run greetings greetings_${environment} --prefix course-js-api-framework"
}
