def installPythonDeps() {
    bat '''
        echo Installing Python dependencies
        if exist python-greetings-install rmdir /s /q python-greetings-install
        git clone https://github.com/mtararujs/python-greetings.git python-greetings-install
        cd python-greetings-install

        echo Creating virtual environment (venv)
        "%PYTHON_PATH%" -m venv venv

        echo Installing pip packages
        call venv\\Scripts\\python -m pip install -r requirements.txt
    '''
}

def deployToEnv(String envName, String port) {
    bat """
        if exist python-greetings-${envName} rmdir /s /q python-greetings-${envName}
        git clone https://github.com/mtararujs/python-greetings.git python-greetings-${envName}
        cd python-greetings-${envName}

        echo Creating venv for ${envName}
        "%PYTHON_PATH%" -m venv venv

        echo Installing pip packages for ${envName}
        call venv\\Scripts\\python -m pip install -r requirements.txt

        echo Deleting old app for ${envName}
        cmd /c "\"%PM2_PATH%\" delete greetings-app-${envName} || exit /b 0"

        echo Starting app on port ${port}
        call "%PM2_PATH%" start app.py --name greetings-app-${envName} --interpreter venv\\Scripts\\python -- --port ${port}
    """
}

def testOnEnv(String envName) {
    bat """
        if exist course-js-api-framework-${envName} rmdir /s /q course-js-api-framework-${envName}
        git clone https://github.com/mtararujs/course-js-api-framework.git course-js-api-framework-${envName}
        cd course-js-api-framework-${envName}

        echo Installing npm packages for ${envName}
        call npm install

        echo Running API tests for ${envName}
        call npm run greetings greetings_${envName}
    """
}

pipeline {
    agent any

    options {
        timestamps()
    }

    environment {
        PYTHON_PATH = 'C:\\Users\\plaki\\AppData\\Local\\Programs\\Python\\Python314\\python.exe'
        PM2_PATH = 'C:\\Users\\plaki\\AppData\\Roaming\\npm\\pm2.cmd'
        PM2_HOME = 'C:\\Users\\plaki\\.pm2'
    }

    stages {
        stage('install-pip-deps') {
            steps {
                echo 'Stage install-pip-deps started.'
                installPythonDeps()
                echo 'Stage install-pip-deps finished.'
            }
        }

        stage('deploy-to-dev') {
            steps {
                echo 'Stage deploy-to-dev started.'
                deployToEnv('dev', '7001')
                echo 'Stage deploy-to-dev finished.'
            }
        }

        stage('tests-on-dev') {
            steps {
                echo 'Stage tests-on-dev started.'
                testOnEnv('dev')
                echo 'Stage tests-on-dev finished.'
            }
        }

        stage('deploy-to-stg') {
            steps {
                echo 'Stage deploy-to-stg started.'
                deployToEnv('stg', '7002')
                echo 'Stage deploy-to-stg finished.'
            }
        }

        stage('tests-on-stg') {
            steps {
                echo 'Stage tests-on-stg started.'
                testOnEnv('stg')
                echo 'Stage tests-on-stg finished.'
            }
        }

        stage('deploy-to-preprod') {
            steps {
                echo 'Stage deploy-to-preprod started.'
                deployToEnv('preprod', '7003')
                echo 'Stage deploy-to-preprod finished.'
            }
        }

        stage('tests-on-preprod') {
            steps {
                echo 'Stage tests-on-preprod started.'
                testOnEnv('preprod')
                echo 'Stage tests-on-preprod finished.'
            }
        }

        stage('deploy-to-prod') {
            steps {
                echo 'Stage deploy-to-prod started.'
                deployToEnv('prod', '7004')
                echo 'Stage deploy-to-prod finished.'
            }
        }

        stage('tests-on-prod') {
            steps {
                echo 'Stage tests-on-prod started.'
                testOnEnv('prod')
                echo 'Stage tests-on-prod finished.'
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully.'
        }
        failure {
            echo 'Pipeline failed.'
        }
        always {
            echo 'Stopping PM2 processes'
            bat '''
                cmd /c ""%PM2_PATH%" delete greetings-app-dev || exit /b 0"
                cmd /c ""%PM2_PATH%" delete greetings-app-stg || exit /b 0"
                cmd /c ""%PM2_PATH%" delete greetings-app-preprod || exit /b 0"
                cmd /c ""%PM2_PATH%" delete greetings-app-prod || exit /b 0"
                cmd /c ""%PM2_PATH%" kill || exit /b 0"
            '''
            echo 'Pipeline finished.'
        }
    }
}
