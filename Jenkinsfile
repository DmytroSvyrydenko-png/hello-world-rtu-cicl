pipeline {
    agent any
    environment {
        PM2_HOME = "C:\\ProgramData\\Jenkins\\.pm2"
    }
    triggers {
        pollSCM('*/1 * * * *')
    }
    stages {
        stage('install-pip-deps') {
            steps {
                script {
                    installPipDeps()
                }
            }
        }
        stage('deploy-to-dev') {
            steps {
                script {
                    deploy('dev', 7001)
                }
            }
        }
        stage('tests-on-dev') {
            steps {
                script {
                    runTests('dev')
                }
            }
        }
        stage('deploy-to-stg') {
            steps {
                script {
                    deploy('stg', 7002)
                }
            }
        }
        stage('tests-on-stg') {
            steps {
                script {
                    runTests('stg')
                }
            }
        }
        stage('deploy-to-preprod') {
            steps {
                script {
                    deploy('preprod', 7003)
                }
            }
        }
        stage('tests-on-preprod') {
            steps {
                script {
                    runTests('preprod')
                }
            }
        }
        stage('deploy-to-prod') {
            steps {
                script {
                    deploy('prod', 7004)
                }
            }
        }
        stage('tests-on-prod') {
            steps {
                script {
                    runTests('prod')
                }
            }
        }
    }
}

def installPipDeps() {
    echo 'Installing all required Python dependencies..'
    git branch: 'main', poll: false, url: 'https://github.com/mtararujs/python-greetings.git'
    bat 'dir'
    bat 'python -m venv venv'
    bat 'venv\\Scripts\\python.exe -m pip install -r requirements.txt'
    echo 'Dependencies successfully installed..'
}

def deploy(String environment, int port) {
    echo "Deployment to ${environment} environment has started.."
    git branch: 'main', poll: false, url: 'https://github.com/mtararujs/python-greetings.git'
    bat 'python -m venv venv'
    bat 'venv\\Scripts\\python.exe -m pip install -r requirements.txt'
    bat "pm2 delete greetings-app-${environment} & EXIT /B 0"
    bat "pm2 start app.py --name greetings-app-${environment} --interpreter \"${env.WORKSPACE}\\venv\\Scripts\\python.exe\" -- --port ${port}"
    echo "Deployment to ${environment} environment finished.."
}

def runTests(String environment) {
    echo "Testing greetings service on ${environment} environment has started.."
    git branch: 'main', poll: false, url: 'https://github.com/mtararujs/course-js-api-framework.git'
    bat 'npm install'
    bat "npm run greetings greetings_${environment}"
    echo "Testing greetings service on ${environment} environment finished.."
}
