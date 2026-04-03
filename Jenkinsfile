#!groovy
import groovy.json.JsonSlurperClassic
node {

    def BUILD_NUMBER=env.BUILD_NUMBER
    def RUN_ARTIFACT_DIR="tests/${BUILD_NUMBER}"
    def SFDC_USERNAME

    def HUB_ORG=env.DEV_ORG_DH
    def SFDC_HOST = env.SFDC_HOST_DH
    def JWT_KEY_CRED_ID = env.JWT_CRED_ID_DH
    def CONNECTED_APP_CONSUMER_KEY=env.CONNECTED_APP_CONSUMER_KEY_DH

    println 'KEY IS' 
    println JWT_KEY_CRED_ID
    println HUB_ORG
    println SFDC_HOST
    println CONNECTED_APP_CONSUMER_KEY
    def toolbelt = "/usr/local/lib/sf/bin/sf"

    stage('checkout source') {
        checkout scm
    }

    withCredentials([file(credentialsId: JWT_KEY_CRED_ID, variable: 'jwt_key_file')]) {
        stage('Deploye Code') {
            def rc
            def rmsg

            if (isUnix()) {
                rmsg = sh returnStdout: true, script: """
                    export HOME="${WORKSPACE}"
                    mkdir -p "\$HOME"

                    ${toolbelt} org login jwt \\
                      --client-id ${CONNECTED_APP_CONSUMER_KEY} \\
                      --username ${HUB_ORG} \\
                      --jwt-key-file ${jwt_key_file} \\
                      --set-default-dev-hub \\
                      --set-default \\
                      --instance-url ${SFDC_HOST}

                    ${toolbelt} org list --all

                    ${toolbelt} project deploy start \\
                      --manifest manifest/package.xml
                """
            } else {
                bat "${toolbelt} update"
                rmsg = bat returnStdout: true, script: """
                    set HOME=%WORKSPACE%
                    ${toolbelt} org login jwt --client-id ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwt-key-file ${jwt_key_file} --loglevel DEBUG --set-default-dev-hub --set-default --instance-url ${SFDC_HOST}
                    ${toolbelt} org list --all
                    ${toolbelt} project deploy start --manifest manifest/package.xml
                """
            }

            printf rmsg
            println('Hello from a Job DSL script!')
            println(rmsg)
        }
    }
}
