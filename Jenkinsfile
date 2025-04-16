pipeline {
    agent any
    triggers {
      pollSCM('*/1 * * * *')
    }
    stages {
        stage('clean') {// pievienoju lai pārliecinātos, ka tiešām ielādē no venv paicņas
            steps {
              cleanWs()
            }
        }
        stage('install-pip-deps') {
            steps {
                echo "Installing all pip dependencies..."
                dir ('app') {
                    git branch : 'main', poll: false, url: 'https://github.com/mtararujs/python-greetings'
                    sh "python3 -m venv .venv"
                    sh ".venv/bin/python3 -m pip install -r requirements.txt"
                }
            }
        }
        // Venv dēļ man nedaudz vajadzēja pamainīt struktūru, jo iznāk, ka šis solis notīrīt venv mapīti
        stage('install-tests') {
            steps {
                echo "Installing all test definitions..."
                dir ('tests') {
                    git branch : 'main', poll: false, url: 'https://github.com/mtararujs/course-js-api-framework' 
                    sh "npm install"
                }
            }
        }
        stage('deploy-to-dev') {
            steps {
                deploy("dev", 7001)
            }
        }
        stage('tests-on-dev') {
            steps {
                test("dev")
            }
        }
        stage('deploy-to-stg') {
            steps {
                deploy("stg", 7002)
            }
        }
        stage('tests-on-stg') {
            steps {
                test("stg")
            }
        }
        stage('deploy-to-preprod') {
            steps {
                deploy("preprod", 7003)
            }
        }
        stage('tests-on-preprod') {
            steps {
                test("preprod")
            }
        }
        stage('deploy-to-prod') {
            steps {
                deploy("prod", 7004)
            }
        }
        stage('tests-on-prod') {
            steps {
                test("prod")
            }
        }
    }
}

def deploy(String env, int port){
    echo "Deploying the app to ${ env } environment..."
    dir ('app') {
        sh "pm2 delete 'greetings-app-${ env }' || true"
        sh 'ls'
        sh "pm2 start app.py --name 'greetings-app-${ env }' --interpreter .venv/bin/python3 -- --port ${ port }"
    }
}

def test(String env){
    echo "Testing the app on ${ env } environment..."
    dir ('tests') {
        sh "npm run greetings greetings_${ env }"
    }
}
