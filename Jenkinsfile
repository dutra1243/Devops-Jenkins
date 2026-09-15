pipeline {
    agent any
 
    options {
        timestamps()
    }

    parameters {
        choice(name: 'ENTORNO', choices: ['dev', 'qa',  'prod'], description: 'Ambiente destino')
        string(name: 'VERSION', defaultValue: '1.0.0', description: 'Versión a desplegar')
        booleanParam(name: 'EJECUTAR_TESTS', defaultValue: true, description: 'Correr los test')

    }
 
    stages {
        stage('Instalar dependencias') {
            steps {
                sh '''
                    python3 -m venv .venv
                    . .venv/bin/activate
                    pip install --quiet -r requirements.txt
                '''
            }
        }
 
        stage('Lint') {
            steps {
                sh '''
                    . .venv/bin/activate
                    ruff check .
                '''
            }
        }
 
        stage('Test') {
            when (
                expression { params.EJECUTAR_TESTS }
            )
            steps {
                sh '''
                    . .venv/bin/activate
                    pytest --junitxml=reports/junit.xml
                '''
            }
        }

        stage('Aprobación') {
            when (
                expression { params.ENTORNO == 'prod' }
            )
            steps {
                input message: '¿Desea desplegar en producción?', ok: 'Desplegar'
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                    echo "Desplegando versión ${VERSION} en el entorno ${ENTORNO}"
                '''
            }
        }
    }
 
    post {
        always {
            junit 'reports/junit.xml'
        }
    }
}