pipeline {
    agent ansible

    environment {
        PLAYBOOK_PATH = "playbooks/main.yml"
        LOG_FILE = "ansible_run.log"
    }
    stages {
        stage ('System Preparation') {
            steps {
                echo "Updating system and installing dependencies..."
                sh '''
                    sudo dnf update -y
                    sudp dnf install -y git python3-pip mariadb105-client

                '''
            }
        }
        stage ('Checkout Repository') {
            steps {
                echo "Checking out Ansible project..."
                checkout scm
            }

        }
        stage ('Install Python Dependencies') {
            steps {
                echo "Installing Ansible Galaxy collections..."
                sh '''
                    ansible-galaxy collection install -r collections/requirements.yml
                '''
            }
        }
        stage ('Run Ansible Playbook') {
            steps {
                echo "Running Ansible playbook using local ansible.cfg..."
                sh '''
                    ansible-playbook ${PLAYBOOK_PATH} -vvv | tee ${LOG_FILE}
                '''
            }
        }
        stage('Archive Logs') {
                    steps {
                        echo "Archiving Ansible logs..."
                        archiveArtifacts artifacts: 'ansible_run.log', onlyIfSuccessful: false
                    }
        }        
    }
    post {
        success {
            echo "✅ Playbook executed successfully!"
        }
        failure {
            echo "❌ Playbook failed. Check ansible_run.log for details."
        }
        always {
            clearWs()
        }
    }

}
