pipeline {
    agent { node { label 'AGENT-1' } }
    options {
        buildDiscarder(logRotator(numToKeepStr: '5'))
    }
    environment{
        //here if you create any variable you will have global access, since it is environment no need of def
        packageVersion = '2.0.0' 
    }
    stages {
        // stage('Get version'){
        //     steps{
        //         script{
        //             def packageJson = readJSON(file: 'package.json')
        //             packageVersion = packageJson.version
        //             echo "version: ${packageVersion}"
        //         }
        //     }
        // }


        /* stage('Get version') {
            steps {
                script {
                    if (fileExists('package.json')) {
                        def packageJson = readJSON(file: 'package.json')
                        def packageVersion = packageJson.version
                        echo "version: ${packageVersion}"
                    } else {
                        error "package.json not found!"
                    }
                }
            }
        } */

        stage('Install depdencies') {
            steps {
                sh '''
                npm install
                '''
            }
        }
        stage('Unit test') {
            steps {
                echo "unit testing is done here"
            }
        }
        //sonar-scanner command expect sonar-project.properties should be available
        stage('Sonar Scan') {
            steps {
                echo "Sonar scan done"
            }
        }
        stage('Build') {
            steps {
                sh 'ls -ltr'
                sh 'zip -r catalogue.zip ./* --exclude=.git --exclude=.zip'
            }
        }
        stage('SAST') {
            steps {
                sh'''
                echo "SAST Done ok"
                echo "package version: $packageVersion"
                '''
            }
        }
        //install pipeline utility steps plugin, if not installed
        stage('Publish Artifact') {
            steps {
                nexusArtifactUploader(
                    nexusVersion: 'nexus3',
                    protocol: 'http',
                    nexusUrl: '54.145.5.153:8081/',
                    groupId: 'com.roboshop',
                    version: "$packageVersion",
                    repository: 'catalogue',
                    credentialsId: 'nexus-auth',
                    artifacts: [
                        [artifactId: 'catalogue',
                        classifier: '',
                        file: 'catalogue.zip',
                        type: 'zip']
                    ]
                )
            }
        }

        //here I need to configure downstram job. I have to pass package version for deployment
        // This job will wait until downstrem job is over
        /* stage('Deploy') {
            steps {
                script{
                    echo "Deployment"
                    def params = [
                        string(name: 'version', value: "$packageVersion")
                    ]
                    build job: "../catalogue-deploy", wait: true, parameters: params
                }
            }
        } */
    }

    post{
        always{
            echo 'cleaning up workspace'
            //deleteDir()
        }
    }
}