pipeline {
    agent {
        label 'AGENT-1'
    }
    options {
        timeout(time: 30, unit: 'MINUTES')
        disableConcurrentBuilds() // No Multiple  Builds
        ansiColor('xterm')
    }
    parameters{
        booleanParam(name: 'deploy', defaultValue: false, description: 'Toggle this value')
    }
    environment{
        def appVersion = '' // variable declaration in GLOBAL LEVEL
        nexusUrl = 'nexus.dawsmani.site:8081'
        region= "us-east-1"
        account_id= "654654190418"
    }
    stages {
        stage ('read the version'){
            steps{ // Variable can be accessed with in that stage only
                script{ // Groovy Script
                def packageJson = readJSON file: 'package.json'
                appVersion = packageJson.version
                echo "application version: $appVersion"
                }
            }
        }
        stage('Install Dependencies') { // init should happen whether apply or destroy
            steps {
               sh """
                npm install
                ls -ltr
                echo "application version: $appVersion"
               """
            }
        }
        stage('Build'){ // Build == Dependencies + code (zipped)
            steps{
                sh """
                sudo yum install zip -y
                zip -q -r backend-${appVersion}.zip * -x Jenkinsfile -x backend-${appVersion}.zip
                ls -ltr
                """
            } // -q (quit --> No need of un-necessary log in jenkins )   -x exclude those files
        }

        stage('Docker build'){
            steps{
                sh """
                    aws ecr get-login-password --region ${region} | docker login --username AWS --password-stdin ${account_id}.dkr.ecr.${region}.amazonaws.com

                    docker build -t ${account_id}.dkr.ecr.${region}.amazonaws.com/expense-backend:${appVersion} .

                    docker push ${account_id}.dkr.ecr.${region}.amazonaws.com/expense-backend:${appVersion}
                """
            }
        }

        stage('Deploy'){
            steps{
                sh """
                    aws eks update-kubeconfig --region us-east-1 --name expense-dev
                    cd helm
                    sed -i 's/IMAGE_VERSION/${appVersion}/g' values.yaml
                    helm install backend .
                """
            } // 1st helm install backend .   2nd helm upgrade backend .
        }

        // stage('Sonar Scan'){
        //     environment {
        //         scannerHome = tool 'sonar' //referring scanner CLI
        //     }
        //     steps {
        //         script {
        //             withSonarQubeEnv('sonar') { //referring sonar server
        //                 sh "${scannerHome}/bin/sonar-scanner" // Sonar Scanner by default searches wih (sonar-project.properties)
        //             }
        //         }
        //     }
        // }

        // stage("Quality Gate") {
        //     steps {
        //       timeout(time: 30, unit: 'MINUTES') {
        //         waitForQualityGate abortPipeline: true // --? If Quality Gate Fail then fail the PipeLine
        //       }
        //     }
        // }


        //  stage('Nexus Artifact Upload'){
        //     steps{
        //         script{ // Groovy Script for Jenkins
        //             nexusArtifactUploader( // --> This is plugin below code from internet
        //                 nexusVersion: 'nexus3',
        //                 protocol: 'http',
        //                 nexusUrl: "${nexusUrl}", // double quotes --> When using variables
        //                 groupId: 'com.expense',
        //                 version: "${appVersion}",
        //                 repository: "backend",
        //                 credentialsId: 'nexus-auth', 
        //                 // Created in Jenkins -->Manage Jenkins --> Credentials --> System --> Global credentials (unrestricted)
        //                 artifacts: [
        //                     [artifactId: "backend",
        //                     classifier: '',
        //                     file: "backend-" + "${appVersion}" + '.zip', // filename: backend-1.1.0.zip
        //                     type: 'zip']
        //                 ]
        //             )
        //         }
        //     }
        // }

        // stage('Deploy'){ // If this success then CI is success
        // when{ //From Line 13
        //     expression{ // If this expression is true then below steps (backend-deploy) will run
        //         params.deploy
        //     }
        // }
        //     steps{ // Down Stream TO CD
        //         script{
        //             def params = [
        //                 string(name: 'appVersion', value: "${appVersion}")// define here parameter to use in down stream (backend-deploy)
        //             ]
        //             build job: 'backend-deploy', parameters: params, wait: false 
        //         }// false -->don't wait to complete down stream job (I triggered job, I don't wait for down stream to complete) 
        //         // wait: true --> wait until the down stram job is done
        //     }
        // }
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