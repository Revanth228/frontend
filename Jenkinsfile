pipeline {
    agent {
        label 'AGENT-1'
    }
    environment {
        nexusUrl = 'nexus.daws78s-rev.online:8081'
    }
    stages {
        stage('Read the version') {
            steps {
                script {
                    def packageJson = readJSON file: 'package.json'
                    env.appVersion = packageJson.version  // Use `env` to set global variables

                    echo "Application version is ${env.appVersion}"
                }
            }
        }


        stage('Build') {
            steps {
                sh """
                    zip -q -r frontend-${env.appVersion}.zip * -x Jenkinsfile -x frontend-${env.appVersion}.zip
                    ls -ltr
                """
            }
        }

        stage('Nexus Artifact Upload') {
            steps {
                script {
                    nexusArtifactUploader(
                        nexusVersion: 'nexus3',
                        protocol: 'http',
                        nexusUrl: "${nexusUrl}",
                        groupId: 'com.expense',
                        version: "${env.appVersion}",
                        repository: "frontend",
                        credentialsId: 'nexus-auth',
                        artifacts: [
                            [
                                artifactId: "frontend",
                                classifier: '',
                                file: "frontend-${env.appVersion}.zip",
                                type: 'zip'
                            ]
                        ]
                    )
                }
            }
        }

        // stage('Deploy') {
        //     steps {
        //         script {
        //             def params = [
        //                 string(name: 'appVersion', value: "${env.appVersion}")
        //             ]
        //             build job: "frontend-deploy", parameters: params, wait: false
        //         }
        //     }
        // }
    }

    post {
        always {
            deleteDir()
        }
    }
}
