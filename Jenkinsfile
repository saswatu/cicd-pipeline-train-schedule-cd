pipeline {
    agent any
    stages {
        stage ('Build') {
            steps {
                echo 'Running build automation'
                sh './gradlew build --no-daemon'
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
        }
        stage('DepploToStaging') {
            when {
                branch 'master'
            }
            steps {
                withCredentials([usernamePassword(credentialsId: 'webserver_login' , usernameVariable: 'USERNAME' passwordVariable: 'USERPASS')] {
                    sshPublisher(
                        failOnError: true,
                        continueOnError: false,
                        publisher: [
                            sshPublisherDesc(
                                configName: 'staging',
                                sshCredentials: [
                                    username: "$USERNAME",
                                    encryptedPassphrase: "$USERPASS"
                                 ],
                                 transfer: [
                                     sshTransfer(
                                         sourceFiles: 'dist/trainSchedule.zip' ,
                                         remotePrefix: 'dist/',
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
                                    ]
                                  )
                               ]
                             )
                           }
                         }
                      }                                
                      stage('DeployToProduction') {
                          when {
                              branch 'master'
                          }
                          steps {
                              input 'Does the staging environment look ok?'
                              milestone(1)
                               withCredentials([usenamePassword(credentialsId: 'webserver_login',usernameVariable: 'USERNAME',passwordVariable: 'USERPASS')]) {
                                   sshPublisher(
                                       failOnError: true,
                                       continueOnError: false,
                                       pulishers: [
                                           sshPublisherDesc(
                                                configName:'staging',
                                                sshCredentials: [
                                                     username: "$USERNAME",
                                                     encryptedPassphrase: "$USERPASS"
                                                   ],
                                                   transfer: [
                                                       sshTransfer(
                                                           sourceFiles: 'dist/trainSchedule.zip',
                                                           removePrefix: '/dist',
                                                           remoteDirectory: '/tmp',
                                                           execCommand: 'sudo /usr/bin/systemctl stop train-schedule && rm -rf /opt/train-schedule/* && unzip 
                           
                                               
