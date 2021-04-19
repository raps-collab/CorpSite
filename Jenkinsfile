pipeline {
    agent any
    stages {
        stage('CI') {
            steps {
                //snDevOpsStep()
                sh '''
                    export M2_HOME=/usr/local # your Mavan home path
                    export PATH=$PATH:$M2_HOME/bin
                    mvn --version
                '''
                sh '/usr/local/bin/mvn compile'
                sh '/usr/local/bin/mvn verify'
            }
            post {
                success {
                    junit '**/target/surefire-reports/*.xml' 
                }
            }
        }
        stage('UAT deploy') {
            steps {
                //snDevOpsStep()
                sh '''
                    export M2_HOME=/opt/apache-maven-3.6.0 # your Mavan home path
                    export PATH=$PATH:$M2_HOME/bin
                    /usr/local/bin/mvn --version
                '''
                sh '/usr/local/bin/mvn package'

                script {
                    sshPublisher(continueOnError: false, failOnError: true,
                    publishers: [
                        sshPublisherDesc(
                            configName:'CorpSite UAT',
                            verbose: true,
                            transfers: [
                                sshTransfer(
                                    sourceFiles: 'target/globex-web.war',
                                    removePrefix: 'target/',
                                    remoteDirectory: '/opt/tomcat/webapps'
                                )
                            ]
                        )
                    ])
                }
            }
        }
        stage('UAT test') {
            steps {
                snDevOpsStep()
                sh '''
                    export M2_HOME=/opt/apache-maven-3.6.0 # your Mavan home path
                    export PATH=$PATH:$M2_HOME/bin
                    /usr/local/bin/mvn --version
                '''
                sh '/usr/local/bin/mvn compile'
                sh '/usr/local/bin/mvn verify'
            }
            post {
                success {
                    junit '**/target/surefire-reports/*.xml' 
                }
            }
        
            
        }
        stage('deploy') {
            steps {
                //snDevOpsStep()
                snDevOpsChange()
                script {
                    sshPublisher(continueOnError: false, failOnError: true,
                    publishers: [
                        sshPublisherDesc(
                            configName:'CorpSite PROD',
                            verbose: true,
                            transfers: [
                                sshTransfer(
                                    sourceFiles: 'target/globex-web.war',
                                    removePrefix: 'target/',
                                    remoteDirectory: '/opt/tomcat/webapps'
                                )
                            ]
                        )
                    ])
                }
            }
        }
    }
}
