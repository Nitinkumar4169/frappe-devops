pipeline {
    agent any

    options {
        skipDefaultCheckout(true)
    }
    
    tools {
        git 'Default'
    }

    parameters {

        choice(
            name: 'ACTION',
            choices: [
                'health_check',
                'create_site',
                'get_app',
                'install_app',
                'backup',
                'restore_site',
                'migrate_site',
                'drop_site',
                'deploy_app'
            ],
            description: 'Select operation'
        )

        string(
            name: 'SITE_NAME',
            defaultValue: '',
            description: 'Site name'
        )

        string(
            name: 'APP_NAME',
            defaultValue: '',
            description: 'App name'
        )

        string(
            name: 'REPO_URL',
            defaultValue: '',
            description: 'Git repository URL'
        )

        string(
            name: 'BRANCH',
            defaultValue: 'main',
            description: 'Git branch'
        )

        string(
            name: 'DB_BACKUP',
            defaultValue: '',
            description: 'Database backup file'
        )

        string(
            name: 'PUBLIC_BACKUP',
            defaultValue: '',
            description: 'Public files backup'
        )

        string(
            name: 'PRIVATE_BACKUP',
            defaultValue: '',
            description: 'Private files backup'
        )
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }
       stage('Set Build Name') {
           steps {
              script {
                  currentBuild.displayName =
                      "${params.ACTION}-${params.SITE_NAME}"
              }
           }
        }

        stage('Run Ansible') {
            steps {
                 withCredentials([string(credentialsId: 'sudo-pass', variable: 'SUDO_PASS')]) {
                sh """
                cd ansible

                ansible-playbook \
                 -i inventory.ini \
                 playbooks/${ACTION}.yml \
                 --extra-vars "ansible_become=true ansible_become_method=sudo ansible_become_password=${SUDO_PASS}" \
                 -e site_name='${SITE_NAME}' \
                 -e app_name='${APP_NAME}' \
                 -e repo_url='${REPO_URL}' \
                 -e branch='${BRANCH}' \
                 -e restore_db_backup='${DB_BACKUP}' \
                 -e restore_public_backup='${PUBLIC_BACKUP}' \
                 -e restore_private_backup='${PRIVATE_BACKUP}'
                """
                 }
            }
        }
    }

    post {

        success {
            echo "Operation ${params.ACTION} completed successfully"
        }

        failure {
            echo "Operation ${params.ACTION} failed"
        }
    }
}
