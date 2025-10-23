pipeline {
    agent { label 'ansible' }

    environment {
        VENV_PATH     = "/var/lib/jenkins/venv"
        PLAYBOOK_PATH = "playbooks/main.yml"
        LOG_FILE      = "ansible_run.log"
    }

    stages {

        stage('Setup Python Virtualenv') {
            steps {
                echo "🔧 Setting up Python virtual environment for Jenkins user..."
                sh '''
                    # If virtualenv does not exist, create a new one
                    if [ ! -d "${VENV_PATH}" ]; then
                        echo "Creating virtualenv at ${VENV_PATH}..."
                        python3 -m venv ${VENV_PATH}
                    fi

                    # Activate virtualenv and install required packages 
                    source ${VENV_PATH}/bin/activate
                    pip install --upgrade pip
                    pip install ansible boto3 botocore awscli
                    deactivate
                '''
            }
        }

        stage('Checkout Repository') {
            steps {
                echo "📦 Checking out Ansible project from SCM..."
                checkout scm
            }
        }

        stage('Install Ansible Collections') {
            steps {
                echo "📚 Installing Ansible Galaxy collections..."
                sh '''
                    source ${VENV_PATH}/bin/activate
                    ansible-galaxy collection install -r collections/requirements.yml
                    deactivate
                '''
            }
        }

        stage('Run Ansible Playbook') {
            steps {
                echo "🚀 Running Ansible playbook..."
                ansiColor('xterm') {
                    sh '''
                        source ${VENV_PATH}/bin/activate
                        ansible-playbook ${PLAYBOOK_PATH} -vvv | tee ${LOG_FILE}
                        deactivate
                    '''
                }
            }
        }

        stage('Archive Logs') {
            steps {
                echo "🗂️ Archiving Ansible logs..."
                archiveArtifacts artifacts: "${LOG_FILE}", onlyIfSuccessful: false
            }
        }
    }

    post {
        success {
            echo "✅ Playbook executed successfully!"
        }
        failure {
            echo "❌ Playbook failed. Check ${LOG_FILE} for details."
        }
        always {
            cleanWs()
        }
    }
}
