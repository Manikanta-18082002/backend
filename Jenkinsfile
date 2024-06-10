pipeline {
    agent {
        label 'AGENT-1'
    }
    options {
        timeout(time: 30, unit: 'MINUTES')
        disableConcurrentBuilds() // No Multiple  Builds
        ansiColor('xterm')
    }
    stages {
        stage ('read the version'){
            steps{
                def packageJson = readJSON file: 'package.json'
                def appVersion = packageJson.version
            }
        }
        stage('Install Dependencies') { // init should happen whether apply or destroy
            steps {
               sh """
                npm install
                ls -ltr
                echo $appVersion
               """
            }
        }
    }
    post {  //This will catch the event and send Alerts to Mail/Slack
        always { 
            echo 'I will always say Hello again!'
            deleteDir()
        }
        success { 
            echo 'I will run when pipeline is success'
        }
        failure { 
            echo 'I will run when pipeline is failure'
        }
    }
}