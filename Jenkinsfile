pipeline {
    agent any

    // Configure PYTHON_CMD to point to your Python executable.
    // Option 1: Using the Python launcher (commonly available on Windows installations)
    // environment { PYTHON_CMD = "py" }
    // Option 2: Use the full path to python.exe (adjust the path to match your installation)
    environment { 
        PYTHON_CMD = "C:\\Python39\\python.exe"
        // Optionally, add Python and Scripts directories to PATH if needed
        PATH = "C:\\Python39;C:\\Python39\\Scripts;${env.PATH}"
    }

    stages {

        stage('install-pip-deps') {
            steps {
                echo "Installing all necessary dependencies..."
                
                // Checkout the repository containing dependencies (python-greetings)
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: 'main']],
                    userRemoteConfigs: [[url: 'https://github.com/mtararujs/python-greetings.git']]
                ])
                
                // Use a bat block to run the pip install command via the PYTHON_CMD variable.
                bat '''
                    echo Starting dependencies installation...
                    %PYTHON_CMD% -m pip install -r requirements.txt
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

// Deploy application function that also installs the required dependencies using Python pip.
def deployApp(String environment, String appPort) {
    bat """
        echo Deploying to ${environment} environment...
        
        if not exist python-greetings (
            git clone https://github.com/mtararujs/python-greetings.git
        )
        cd python-greetings
        
        call pm2 delete greetings-app-${environment} || exit /b 0
        
        %PYTHON_CMD% -m pip install -r requirements.txt
        call pm2 start app.py --name greetings-app-${environment} -- --port ${appPort}
    """
}

// Run tests function that checks out another repository and performs test commands.
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
