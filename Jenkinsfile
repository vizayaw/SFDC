#!groovy

import groovy.json.JsonSlurperClassic

node {

    def SF_CONSUMER_KEY=env.SF_CONSUMER_KEY
    def SF_USERNAME=env.SF_USERNAME
    def SERVER_KEY_CREDENTALS_ID=env.JWT_CRED_ID_DH  
    def SF_INSTANCE_URL = env.SF_INSTANCE_URL

    def toolbelt = tool 'toolbelt'
	
	println 'KEY IS' 
    println SERVER_KEY_CREDENTALS_ID
    println SF_USERNAME
    println SF_INSTANCE_URL
    println SF_CONSUMER_KEY
    

    // -------------------------------------------------------------------------
    // Check out code from source control.
    // -------------------------------------------------------------------------

    stage('checkout source') {
        checkout scm
        changedFiles = sh(returnStdout: true, script: 'git -P show --pretty="" --name-only').split()
        echo "Changed files are: ${changedFiles}"
        echo  "length: ${changedFiles.length}"
        
    }


    // -------------------------------------------------------------------------
    // Run all the enclosed stages with access to the Salesforce
    // JWT key credentials.
    // -------------------------------------------------------------------------
    
    withEnv(["HOME=${env.WORKSPACE}"]) {
        
        withCredentials([file(credentialsId: SERVER_KEY_CREDENTALS_ID, variable: 'server_key_file')]) {

            // -------------------------------------------------------------------------
            // Authorize the Salesforce Org.
            // -------------------------------------------------------------------------

            stage('Authorize the Salesforce Org') {               
                rc = command "${toolbelt} force:auth:jwt:grant --clientid ${SF_CONSUMER_KEY} --username ${SF_USERNAME} --jwtkeyfile ${server_key_file} --setdefaultdevhubusername --instanceurl ${SF_INSTANCE_URL}"
                if (rc != 0) {
                    error 'Salesforce org authorization failed.'
                }
            }

            // -------------------------------------------------------------------------
            // Deploy the code.
            // -------------------------------------------------------------------------

            stage('Deploy the code') {
                rc = command "${toolbelt} force:source:deploy -p ${changedFiles}  -u ${SF_USERNAME}"
                if (rc != 0) {
                    error 'Deployment failed.'
                }
            }
        }
    }
}

def command(script) {
    if (isUnix()) {
        return sh(returnStatus: true, script: script);
    } else {
        return bat(returnStatus: true, script: script);
    }
}