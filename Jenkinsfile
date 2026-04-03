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
    def ORG_ALIAS = "jenkinsHub"

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
        stage('Validate Code') {
            def rmsg

            if (isUnix()) {
                rmsg = sh returnStdout: true, script: """
                    export HOME="${WORKSPACE}"
                    export SF_USE_GENERIC_UNIX_KEYCHAIN=true
                    mkdir -p "\$HOME"

                    ${toolbelt} org login jwt \\
                      --client-id ${CONNECTED_APP_CONSUMER_KEY} \\
                      --username ${HUB_ORG} \\
                      --jwt-key-file ${jwt_key_file} \\
                      --alias ${ORG_ALIAS} \\
                      --set-default \\
                      --set-default-dev-hub \\
                      --instance-url ${SFDC_HOST}

                    ${toolbelt} org list --all

                    ${toolbelt} project deploy validate \\
                      --manifest manifest/package.xml \\
                      --target-org ${ORG_ALIAS}
                """
            } else {
                bat "${toolbelt} update"
                rmsg = bat returnStdout: true, script: """
                    set HOME=%WORKSPACE%
                    set SF_USE_GENERIC_UNIX_KEYCHAIN=true
                    ${toolbelt} org login jwt --client-id ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwt-key-file ${jwt_key_file} --alias ${ORG_ALIAS} --set-default --set-default-dev-hub --instance-url ${SFDC_HOST}
                    ${toolbelt} org list --all
                    ${toolbelt} project deploy validate --manifest manifest/package.xml --target-org ${ORG_ALIAS}
                """
            }

            printf rmsg
            println(rmsg)
        }
    }
}
