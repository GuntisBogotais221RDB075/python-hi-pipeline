pipeline {
    agent any
    
    stages {
        stage('install-pip-deps') {
            steps {
                echo "Installing all necessary dependencies..."
                
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: 'main']],
                    userRemoteConfigs: [[url: 'https://github.com/mtararujs/python-greetings.git']]
                ])
                
                sh '''
                    echo "Starting dependencies installation..."
                    pip install -r requirements.txt
                    echo "Dependencies successfully installed!"
                '''
            }
        }
        
        stage('deploy-to-dev') {
            steps {
                echo "Deploying to DEV environment..."
                deployApp('dev', '7001')
            }
        }
        
        stage('tests-on-dev') {
            steps {
                echo "Running tests in DEV environment..."
                runTests('dev', '7001')
            }
        }
        
        stage('deploy-to-staging') {
            steps {
                echo "Deploying to STAGING environment..."
                deployApp('staging', '7002')
            }
        }
        
        stage('tests-on-staging') {
            steps {
                echo "Running tests in STAGING environment..."
                runTests('staging', '7002')
            }
        }
        
        stage('deploy-to-preprod') {
            steps {
                echo "Deploying to PREPROD environment..."
                deployApp('preprod', '7003')
            }
        }
        
        stage('tests-on-preprod') {
            steps {
                echo "Running tests in PREPROD environment..."
                runTests('preprod', '7003')
            }
        }
        
        stage('deploy-to-prod') {
            steps {
                echo "Deploying to PROD environment..."
                deployApp('prod', '7004')
            }
        }
        
        stage('tests-on-prod') {
            steps {
                echo "Running tests in PROD environment..."
                runTests('prod', '7004')
            }
        }
    }
}

def deployApp(String environment, String appPort) {
    sh """
        echo "Deploying to ${environment} environment..."
        
        git clone https://github.com/mtararujs/python-greetings.git
        cd python-greetings
        
        pm2 delete greetings-app-${environment} || true
        
        pip install -r requirements.txt
        pm2 start app.py --name greetings-app-${environment} -- --port ${appPort}
    """
}

def runTests(String environment, String appPort) {
    sh """
        echo "Running tests in ${environment} environment..."
        
        git clone https://github.com/mtararujs/course-js-api-framework.git
        cd course-js-api-framework
        npm install
        
        export TEST_ENV="${environment}"
        npm run greetings greetings_${environment}
    """
}
