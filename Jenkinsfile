def installPythonDeps() {
    bat '''
        echo Installing Python dependencies.
        if exist python-greetings rmdir /s /q python-greetings
        git clone https://github.com/mtararujs/python-greetings.git
        cd python-greetings

        echo Creating venv
        "%PYTHON_PATH%" -m venv venv

        echo Installing pip packages into venv.
        call venv\\Scripts\\python -m pip install -r requirements.txt
    '''
}

def deployToEnv(String envName, String port) {
    bat """
        echo === DEPLOY STAGE ${envName} ===
        if exist python-greetings rmdir /s /q python-greetings
        git clone https://github.com/mtararujs/python-greetings.git
        cd python-greetings

        echo Creating venv for ${envName}
        "%PYTHON_PATH%" -m venv venv

        echo Installing pip packages for ${envName}
        call venv\\Scripts\\python -m pip install -r requirements.txt

        echo Deleting old PM2 app for ${envName}
        cmd /c "pm2 delete greetings-app-${envName} || exit /b 0"

        echo Starting app on port ${port}
        call pm2 start app.py --name greetings-app-${envName} --interpreter venv\\Scripts\\python -- --port ${port}

        echo PM2 list:
        call pm2 list
    """
}

pipeline {
    agent any

    options {
        timestamps()
    }

    environment {
        PYTHON_PATH = 'C:\\Users\\plaki\\AppData\\Local\\Programs\\Python\\Python314\\python.exe'
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
    }

    post {
        success {
            echo 'Pipeline completed successfully. All deployments and tests passed.'
        }
        failure {
            echo 'Pipeline failed. Check stage logs for details.'
        }
        always {
            echo 'Pipeline execution finished.'
        }
    }
}
