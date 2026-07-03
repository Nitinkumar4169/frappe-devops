pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Ansible Deploy') {
            steps {
                sh '''
                cd ansible
                ansible-playbook playbooks/deploy_app.yml
                '''
            }
        }
    }
}
