pipeline {
    agent any

    // just a check
    environment {
        PATH = "C:\\Python39;C:\\Python39\\Scripts;${env.PATH}"
    }

    stages {
        stage('install-pip-deps') {
            steps {
                echo "Installing all necessary dependencies..."
                
                // Checkout the python-greetings repository
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: 'main']],
                    userRemoteConfigs: [[url: 'https://github.com/mtararujs/python-greetings.git']]
                ])
                
                // Directly run commands in a bat multiline block.
                bat '''
                    echo Starting dependencies installation...
                    python -m pip install -r requirements.txt
                    echo Dependencies successfully installed!
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
    bat """
        echo Deploying to ${environment} environment...
        
        if not exist python-greetings (
            git clone https://github.com/mtararujs/python-greetings.git
        )
        cd python-greetings
        
        call pm2 delete greetings-app-${environment} || exit /b 0
        
        python -m pip install -r requirements.txt
        call pm2 start app.py --name greetings-app-${environment} -- --port ${appPort}
    """
}

def runTests(String environment, String appPort) {
    bat """
        echo Running tests in ${environment} environment...
        
        if not exist course-js-api-framework (
            git clone https://github.com/mtararujs/course-js-api-framework.git
        )
        cd course-js-api-framework
        call npm install
        
        set TEST_ENV=${environment}
        call npm run greetings greetings_${environment}
    """
}
