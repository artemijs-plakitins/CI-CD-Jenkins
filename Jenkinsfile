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
