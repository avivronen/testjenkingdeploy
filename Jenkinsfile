pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation'
                sh 'chmod +x ./gradlew'
                sh './gradlew build --no-daemon'
                archiveArtifacts artifacts: 'dist/test.zip'
            }
        }
        stage('DeployToDev') {
            when {
                branch 'dev'
            }
            steps {
                withCredentials([usernamePassword(credentialsId: 'webservers_deployyser_login', usernameVariable: 'USERNAME', passwordVariable: 'USERPASS')]) {
                    sshPublisher(
                        failOnError: true,
                        continueOnError: false,
                        publishers: [
                            sshPublisherDesc(
                                configName: 'dev',
                                sshCredentials: [
                                    username: "$USERNAME",
                                    encryptedPassphrase: "$USERPASS"
                                ], 
                                transfers: [
                                    sshTransfer(
                                        sourceFiles: 'dist/test.zip',
                                        removePrefix: 'dist/',
                                        remoteDirectory: '/tmp',
                                        execCommand: 'rm -rf /var/www/* && unzip /tmp/test.zip -d /var/www'
                                    )
                                ]
                            )
                        ]
                    )
                }
            }
        }
        stage('DeployToStaging') {
            when {
                branch 'master'
            }
            steps {
                withCredentials([usernamePassword(credentialsId: 'webservers_deployyser_login', usernameVariable: 'USERNAME', passwordVariable: 'USERPASS')]) {
                    sshPublisher(
                        failOnError: true,
                        continueOnError: false,
                        publishers: [
                            sshPublisherDesc(
                                configName: 'staging',
                                sshCredentials: [
                                    username: "$USERNAME",
                                    encryptedPassphrase: "$USERPASS"
                                ], 
                                transfers: [
                                    sshTransfer(
                                        sourceFiles: 'dist/test.zip',
                                        removePrefix: 'dist/',
                                        remoteDirectory: '/tmp',
                                        execCommand: 'rm -rf /var/www/* && unzip /tmp/test.zip -d /var/www'
                                    )
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
                input 'Does the staging environment look OK?'
                milestone(1)
                withCredentials([usernamePassword(credentialsId: 'webservers_deployyser_login', usernameVariable: 'USERNAME', passwordVariable: 'USERPASS')]) {
                    sshPublisher(
                        failOnError: true,
                        continueOnError: false,
                        publishers: [
                            sshPublisherDesc(
                                configName: 'production',
                                sshCredentials: [
                                    username: "$USERNAME",
                                    encryptedPassphrase: "$USERPASS"
                                ], 
                                transfers: [
                                    sshTransfer(
                                        sourceFiles: 'dist/test.zip',
                                        removePrefix: 'dist/',
                                        remoteDirectory: '/tmp',
                                        execCommand: 'rm -rf /var/www/* && unzip /tmp/test.zip -d /var/www'
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
