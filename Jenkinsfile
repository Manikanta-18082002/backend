pipeline {
    agent {
        label 'AGENT-1'
    }
    options {
        timeout(time: 30, unit: 'MINUTES')
        disableConcurrentBuilds() // No Multiple  Builds
        ansiColor('xterm')
    }
    stages{
        stage('test'){
            steps{
                sh """
                echo "This is Testing"
                ls -ltr
                """
            }
        }
    }



    // environment{
    //     def appVersion = '' // variable declaration
    //     nexusUrl = 'nexus.dawsmani.site:8081'
    // }
    // stages {
    //     stage ('read the version'){
    //         steps{ // Variable can be accessed with in that stage only
    //             script{ // Groovy Script
    //             def packageJson = readJSON file: 'package.json'
    //             appVersion = packageJson.version
    //             echo "application version: $appVersion"
    //             }
    //         }
    //     }
    //     stage('Install Dependencies') { // init should happen whether apply or destroy
    //         steps {
    //            sh """
    //             npm install
    //             ls -ltr
    //             echo "application version: $appVersion"
    //            """
    //         }
    //     }
    //     stage('Build'){ // Build == Dependencies + code (zipped)
    //         steps{
    //             sh """
    //             zip -q -r backend-${appVersion}.zip * -x Jenkinsfile -x backend-${appVersion}.zip
    //             ls -ltr
    //             """
    //         } // -q (quit --> No need of un-necessary log)
    //     }
    //     stage('Nexus Artifact Upload'){
    //         steps{
    //             script{ // Groovy Script for Jenkins
    //                  nexusArtifactUploader( // --> This is plugin below code from internet
    //                     nexusVersion: 'nexus3',
    //                     protocol: 'http',
    //                     nexusUrl: "${nexusUrl}", // double quotes --> When using variables
    //                     groupId: 'com.expense',
    //                     version: "${appVersion}",
    //                     repository: "backend",
    //                     credentialsId: 'nexus-auth', // Created in Jenkins
    //                     artifacts: [
    //                         [artifactId: "backend" ,
    //                         classifier: '',
    //                         file: "backend-" + "${appVersion}" + '.zip', // filename: backend-1.1.0.zip
    //                         type: 'zip']
    //                     ]
    //                 )
    //             }
    //         }
    //     }
    //     stage('Deploy'){ // If this success then CI is success
    //         steps{
    //             script{
    //                 def params = [
    //                     string(name: 'appVersion', value: "${appVersion}")// define here parameter to use in down stream (backend-deploy)
    //                 ]
    //                 build job: 'backend-deploy', parameters: params, wait: false 
    //             }// false -->don't wait to complete down stream job (I triggered job, I don't wait for down stream to complete the job) 
    //             // wait: true --> wait until the down stram job is done
    //         }
    //     }
    // }
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