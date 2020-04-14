pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation'
                sh './gradlew build --no-daemon'
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
        }
        stage('DeploytoStaging')
        { // run only when the code is merged to branch 'master'
            when {
                branch 'solution-adityabhimireddy'
            }
         // steps executed on the server
            steps{
                // 1. Set server credentials using sshPublisherDesc
                // 2. Transfer files using sshTransfer
         // credentials wrapper with the credentials named as 'webserver_login'
         // setup usernameVariable as 'USERNAME' which can be used in the steps below
         // setup passwordVariable as 'USERPASS' which can be used in the steps below
                withCredentials([usernamePassword(credentialsId: 'webserver_login', usernameVariable: 'USERNAME', passwordVariable: 'USERPASS')]){
                    sshPublisher(
                        failOnError: true, // determines if the pipeline should proceed on failure
                        continueOnError: false, // determines if the pipeline should proceed on error
                        publishers:[
                            sshPublisherDesc( // desc of user information used to connect to the server
                                configName: 'staging', // name of the staging server name set while configuring Jenkins
                                sshCredentials: [ // credentials allowing jenkins to connect to staging/production server
                                    username: '$USERNAME',
                                    encryptedPassphrase: '$USERPASS'
                                ],
                                transfers: [
                                    sshTransfer(
                                        sourceFiles: 'dist/trainSchedule.zip',
                                        removePrefix: 'dist/',
                                        remoteDirectory: '/tmp',
                                        execCommand: 'sudo /usr/bin/systemctl stop train-schedule && rm -rf /opt/train-schedule/* && unzip /tmp/trainSchedule.zip -d /opt/train-schedule && sudo /usr/bin/systemctl start train-schedule'
                                    )
                                ]

                            )
                        ]
                    )
                }
            }

        }
        stage('DeploytoProduction')
        { // run only when the code is merged to branch 'master'
            when {
                branch 'solution-adityabhimireddy'
            }
         // steps executed on the server
            steps{
                input 'Does the staging code look ok ?'
                milestone(1)
                // 1. Set server credentials using sshPublisherDesc
                // 2. Transfer files using sshTransfer
         // credentials wrapper with the credentials named as 'webserver_login'
         // setup usernameVariable as 'USERNAME' which can be used in the steps below
         // setup passwordVariable as 'USERPASS' which can be used in the steps below
                withCredentials([usernamePassword(credentialsId: 'webserver_login', usernameVariable: 'USERNAME', passwordVariable: 'USERPASS')]){
                    sshPublisher(
                        failOnError: true, // determines if the pipeline should proceed on failure
                        continueOnError: false, // determines if the pipeline should proceed on error
                        publishers:[
                            sshPublisherDesc( // desc of user information used to connect to the server
                                configName: 'production', // name of the staging server name set while configuring Jenkins
                                sshCredentials: [ // credentials allowing jenkins to connect to staging/production server
                                    username: '$USERNAME',
                                    encryptedPassphrase: '$USERPASS'
                                ],
                                transfers: [
                                    sshTransfer(
                                        sourceFiles: 'dist/trainSchedule.zip',
                                        removePrefix: 'dist/',
                                        remoteDirectory: '/tmp',
                                        execCommand: 'sudo /usr/bin/systemctl stop train-schedule && rm -rf /opt/train-schedule/* && unzip /tmp/trainSchedule.zip -d /opt/train-schedule && sudo /usr/bin/systemctl start train-schedule'
                                    )
                                ]

                            )
                        ]
                    )
                }
            }

        }

    }
}
}