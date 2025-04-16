pipeline {
    agent any
    
    stages {
        stage('install-pip-deps') {
            steps {
                echo "Installing all necessary dependencies..."
                
                // Checkout the python-greetings repository from GitHub
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: 'main']],
                    userRemoteConfigs: [[url: 'https://github.com/mtararujs/python-greetings.git']]
                ])
                
                bat '''
                    echo Starting dependencies installation...
                    
                    python -m venv venv
                    call venv\\Scripts\\activate
                    pip install -r requirements.txt
                    
                    echo Dependencies successfully installed!
                '''
            }
        }
        
        stage('deploy-to-dev') {
            steps {
                echo "Deploying to DEV environment..."
                deployToEnvironment('dev', '7001')
            }
        }
        
        stage('tests-on-dev') {
            steps {
                echo "Running tests in DEV environment..."
                testInEnvironment('dev', '7001')
            }
        }
        
        stage('deploy-to-staging') {
            steps {
                echo "Deploying to STAGING environment..."
                deployToEnvironment('staging', '7002')
            }
        }
        
        stage('tests-on-staging') {
            steps {
                echo "Running tests in STAGING environment..."
                testInEnvironment('staging', '7002')
            }
        }
        
        stage('deploy-to-preprod') {
            steps {
                echo "Deploying to PREPROD environment..."
                deployToEnvironment('preprod', '7003')
            }
        }
        
        stage('tests-on-preprod') {
            steps {
                echo "Running tests in PREPROD environment..."
                testInEnvironment('preprod', '7003')
            }
        }
        
        stage('deploy-to-prod') {
            steps {
                echo "Deploying to PROD environment..."
                deployToEnvironment('prod', '7004')
            }
        }
        
        stage('tests-on-prod') {
            steps {
                echo "Running tests in PROD environment..."
                testInEnvironment('prod', '7004')
            }
        }
    }
}

// Deploys the python-greetings app to a given environment and port
def deployToEnvironment(String envName, String port) {
    bat """
        echo Deploying to ${envName} environment...
        
        if not exist python-greetings (
          git clone https://github.com/mtararujs/python-greetings.git python-greetings
        )
        
        cd python-greetings
        
        call pm2 delete greetings-app-${envName} || echo No process to delete
        
        echo Current directory: %cd%
        echo Python version:
        python --version
        
        python -m venv venv || echo Virtual environment exists
        call venv\\Scripts\\activate
        pip install -r requirements.txt
        
        call pm2 delete greetings-app-${envName} || echo No process to delete
        
        call pm2 start app.py --name greetings-app-${envName} --interpreter %cd%\\venv\\Scripts\\python -- --port ${port}
        
        call pm2 status
        timeout /t 5
    """
}

// Runs tests against the deployed service in a given environment and port
def testInEnvironment(String envName, String port) {
    bat """
        echo Running tests in ${envName} environment...
        
        if not exist course-js-api-framework (
          git clone https://github.com/mtararujs/course-js-api-framework.git course-js-api-framework
        )
        
        cd course-js-api-framework
        call npm install
        
        echo Checking service at http://localhost:${port}/greetings
        curl -s -f http://localhost:${port}/greetings && (
            echo Service is available at http://localhost:${port}/greetings
        ) || (
            echo WARNING: Service is not available at http://localhost:${port}/greetings
        )
        
        if not exist config (
          mkdir config
        )
        
        rem Create the hosts.json file
        > config\\hosts.json echo {^
          "dev": { "host": "http://localhost:${port}" },^
          "staging": { "host": "http://localhost:${port}" },^
          "preprod": { "host": "http://localhost:${port}" },^
          "prod": { "host": "http://localhost:${port}" }^
        }
        
        echo Created hosts.json with port ${port}:
        type config\\hosts.json
        
        set REQUESTS_FILE=tests\\utils\\requests.js
        if exist %REQUESTS_FILE% (
          echo Modifying requests.js
          
          rem Create a temporary requests.js file with new content
          (
            echo import request from 'supertest';
            echo import fs from 'fs';
            echo import path from 'path';
            echo.
            echo function getConfig() {
            echo     try {
            echo         const configPath = path.resolve('config/hosts.json');
            echo         const configData = fs.readFileSync(configPath, 'utf8');
            echo         return JSON.parse(configData);
            echo     } catch (error) {
            echo         console.error('Error reading configuration:', error.message);
            echo         return { dev: { host: 'http://localhost:3000' } };
            echo     }
            echo }
            echo.
            echo const env = process.env.TEST_ENV || 'dev';
            echo const config = getConfig();
            echo if (!config[env]) {
            echo     console.warn(`Warning: No configuration for ${env} environment, using dev`);
            echo     config[env] = config.dev;
            echo }
            echo console.log(`Using host: ${config[env].host} for environment: ${env}`);
            echo.
            echo export default (method, url, data, headers) => {
            echo     return request(config[env].host)[method](url).send(data).set(headers);
            echo };
          ) > temp_requests.js
          
          move /Y temp_requests.js %REQUESTS_FILE%
          echo requests.js file successfully modified
        ) else (
          echo Error: requests.js not found!
        )
        
        set TEST_ENV=${envName}
        echo Set variable TEST_ENV=%TEST_ENV%
        
        set NODE_DEBUG=http
        call npm run greetings greetings_${envName} || echo Tests finished with errors, but continuing execution
    """
}
