#!groovy
import groovy.json.JsonSlurperClassic
node ('SF-Slave') {

    def BUILD_NUMBER=env.BUILD_NUMBER
    def RUN_ARTIFACT_DIR="tests/${BUILD_NUMBER}"
    def SFDC_USERNAME

    def HUB_ORG=env.SF_USERNAME
    def SFDC_HOST = env.SF_INSTANCE_URL
    def JWT_KEY_CRED_ID = 'SFDCCert3'
    def CONNECTED_APP_CONSUMER_KEY=env.SF_CONSUMER_KEY

    println 'KEY IS' 
    println JWT_KEY_CRED_ID
    println HUB_ORG
    println SFDC_HOST
    println CONNECTED_APP_CONSUMER_KEY
    def toolbelt = tool 'toolbelt'

    stage('checkout source') {
        // when running in multi-branch job, one must issue this command
        checkout scm
    }

    withCredentials([file(credentialsId: JWT_KEY_CRED_ID, variable: 'jwt_key_file')]) {
        stage('Deploye Code') {
            if (isUnix()) {
                rc = sh returnStatus: true, script: "sfdx version"
            }else{
                 rc = bat returnStatus: true, script: "\"${toolbelt}\" force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwtkeyfile \"${jwt_key_file}\" --setdefaultdevhubusername --instanceurl ${SFDC_HOST}"
            }
            if (rc != 0) { error 'Salesforce org authorization failed' }

			println rc
			
					  
            printf rmsg
            println('Hello from a Job DSL script!')
            println(rmsg)
        }
    }
}
