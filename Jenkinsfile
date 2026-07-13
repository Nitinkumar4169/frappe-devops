pipeline {
    agent any

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

        stage('Run Ansible') {
            steps {

                sh """
                cd ansible

                ansible-playbook \
                 -i inventory.ini \
                 playbooks/${ACTION}.yml \
                -e site_name='${SITE_NAME}' \
                -e app_name='${APP_NAME}' \
                -e repo_url='${REPO_URL}' \
                -e branch='${BRANCH}' \
                -e db_backup='${DB_BACKUP}' \
                -e public_backup='${PUBLIC_BACKUP}' \
                -e private_backup='${PRIVATE_BACKUP}'
                """
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
