def app = [appName: 'example-app',   appPort: '8080']

pipeline {


    /* Environment variables related to docker hub */
    environment {
        registry = "nisumpk"
        registryCredential = 'docker-hub-credentials'
        dockerHost = 'localhost'
    }

    agent any

    tools {
        maven 'apache-maven'
    }

    stages {

        stage('Clone Application') {
            /* Git checkout */
            steps {
                checkout([$class: 'GitSCM',
                branches: [[name: '*/master']],
                doGenerateSubmoduleConfigurations: false,
                extensions: [[$class: 'CleanCheckout']],
                submoduleCfg: [],
                userRemoteConfigs: [[credentialsId: '<git-credentials>', url: 'https://github.com/fqadeer-nisum-com/the-example-app.java.git']]
          
                ])
            }
        }

        stage('Build Application') {
            steps {
                sh "cd ${WORKSPACE} && ./gradlew build"
            }
        }

        stage('Building Images') {
            steps {
                script {
                    /* Copying jar file to relavant directory */
                    sh script: "cp  ${WORKSPACE}/application.properties  ${WORKSPACE}/build/resources/main/"
                    sh script: "cp -r ${WORKSPACE}/build ${WORKSPACE}/build.gradle ${WORKSPACE}/gradle ${WORKSPACE}/gradle.properties ${WORKSPACE}/gradlew ${WORKSPACE}/src ${WORKSPACE}/install/deploy/docker/" + app.appName.replaceAll("-", "_") + "/"
                    sh script: "cd ${WORKSPACE}/install/deploy/docker/" + app.appName.replaceAll("-", "_") + " && tar -cvf ../example_app.tar.gz *"

                    /* Building docker image */
                    docker.build(registry + "/" + app.appName.replaceAll("-", "_"), "-f ${WORKSPACE}/install/deploy/docker/" + app.appName.replaceAll("-", "_") + "/Dockerfile ${WORKSPACE}/install/deploy/docker")
                }
            }
        }

        stage('Tagging Images') {
            steps {
                script {
                    commit= sh (returnStatus: true, script: "git log -1 --pretty=%h > gitOutput.txt")
                    gitStatus = readFile('gitOutput.txt').trim()
                    appVersion = readFile('version').trim()

                    /* Assigning tag as Jenkins git pretty name and Build_Number for tracking pupose */
                    sh script: "docker tag " + registry + "/" + app.appName.replaceAll("-", "_") + ":latest " + registry + "/" + app.appName.replaceAll("-", "_") + ":" + appVersion + "_${BUILD_NUMBER}_" + gitStatus
                }
            }
        }

        stage('Push Images To Docker-Hub') {
            steps {
                script{
                    commit= sh (returnStatus: true, script: "git log -1 --pretty=%h > gitOutput.txt")
                    gitStatus = readFile('gitOutput.txt').trim()
                    appVersion = readFile('version').trim()

                    try {
                        /* Uploading image to docker hub using docker cli */
                        withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: "${env.registryCredential}",
                            usernameVariable: 'dockerUser', passwordVariable: 'dockerPassword']]) {
                                sh "docker login -u$dockerUser -p$dockerPassword; docker push ${env.registry}/" + app.appName.replaceAll("-", "_") + ":" + appVersion + "_${BUILD_NUMBER}_" + gitStatus + ";docker push ${env.registry}/" + app.appName.replaceAll("-", "_") + ":latest"
                        }
                    } finally {
                        /* logging out docker account */
                            sh "docker logout"
                    }
                }
            }
        }



    }
}
