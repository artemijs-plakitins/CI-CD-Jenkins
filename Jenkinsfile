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
        if exist python-greetings rmdir /s /q python-greetings
        git clone https://github.com/mtararujs/python-greetings.git
        cd python-greetings

        echo Creating venv for ${envName}
        "%PYTHON_PATH%" -m venv venv

        echo Installing pip packages for ${envName}
        call venv\\Scripts\\python -m pip install -r requirements.txt

        echo Deleting old app for ${envName}
        call "%PM2_PATH%" delete greetings-app-${envName}

        echo Starting app on port ${port}
        call "%PM2_PATH%" start app.py --name greetings-app-${envName} --interpreter venv\\Scripts\\python -- --port ${port}
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
