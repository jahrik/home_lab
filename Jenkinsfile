stage 'Checking connectivity'

node {
    deleteDir()
    checkout scm
    
    wrap([$class: 'AnsiColorBuildWrapper', colorMapName: "xterm"]) {
        ansiblePlaybook(
            playbook: 'ping.yml',
            inventory: 'inventory.ini',
            // credentialsId: '96b3fe82-e6a4-45eb-9e8d-0a512cba5a9c',
            colorized: true)
    }
}

// stage "Check syntax"
// 
// node {
//     wrap([$class: 'AnsiColorBuildWrapper', colorMapName: "xterm"]) {
//         ansiblePlaybook(
//             playbook: 'webserver.yml',
//             inventory: 'inventory.ini',
//             credentialsId: '96b3fe82-e6a4-45eb-9e8d-0a512cba5a9c',
//             extras: '--syntax-check',
//             colorized: true
//             )
//     }
// }

// stage "Check dry-run"
// 
// node {
//     wrap([$class: 'AnsiColorBuildWrapper', colorMapName: "xterm"]) {
//         ansiblePlaybook(
//             playbook: 'webserver.yml',
//             inventory: 'inventory.ini',
//             credentialsId: '96b3fe82-e6a4-45eb-9e8d-0a512cba5a9c',
//             extras: '--check --diff',
//             colorized: true
//             )
//     }
// }

stage "Deploy to production"

node {
    wrap([$class: 'AnsiColorBuildWrapper', colorMapName: "xterm"]) {
        ansiblePlaybook(
            playbook: 'shredder.yml',
            inventory: 'inventory.ini',
            // credentialsId: '96b3fe82-e6a4-45eb-9e8d-0a512cba5a9c',
            colorized: true
            )
    }
}
