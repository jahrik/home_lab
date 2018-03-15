#!/usr/bin/env groovy

node('master') {
    try {
        stage('build') {
            // Clean workspace
            deleteDir()
            // Checkout the app at the given commit sha from the webhook
            checkout scm
        }

        stage('test') {
            // Run any testing suites
        }

        stage('deploy') {

            // If we had ansible installed on the server, setup to run an ansible playbook
            // sh "ansible-playbook -i ./ansible/hosts ./ansible/deploy.yml"
            ansiblePlaybook(
                playbook: 'shredder.yml',
                inventory: 'inventory.ini',
                // credentialsId: '96b3fe82-e6a4-45eb-9e8d-0a512cba5a9c',
                colorized: true
                )

            sh "echo 'WE ARE DEPLOYING'"

        }
    } catch(error) {
        throw error
    } finally {
        // Any cleanup operations needed, whether we hit an error or not
    }

}
