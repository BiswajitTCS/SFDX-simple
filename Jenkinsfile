#!groovy
import groovy.json.JsonSlurperClassic
node {

    def BUILD_NUMBER=env.BUILD_NUMBER
    def RUN_ARTIFACT_DIR="tests/${BUILD_NUMBER}"
    def SFDC_USERNAME

    def HUB_ORG=env.HUB_ORG_DH
    def SFDC_HOST = env.SFDC_HOST_DH
    def JWT_KEY_CRED_ID = env.JWT_CRED_ID_DH
    def CONNECTED_APP_CONSUMER_KEY=env.CONNECTED_APP_CONSUMER_KEY_DH

    def toolbelt = tool 'toolbelt'

    stage('checkout source') {
        // when running in multi-branch job, one must issue this command
        checkout scm
    }

    withCredentials([file(credentialsId: JWT_KEY_CRED_ID, variable: 'jwt_key_file')]) {
        stage('Create Package') {

            rc = bat returnStatus: true, script: "sfdx force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwtkeyfile ${jwt_key_file} --setdefaultdevhubusername --instanceurl ${SFDC_HOST}"
            if (rc != 0) { error 'hub org authorization failed' }

             // need to pull out assigned username
            rmsg = bat returnStdout: true, script: "sfdx force:package:create --name "MyDXPackage" --description "MyDXPackage" --packagetype Unlocked"
            printf rmsg
                
            rmsg = bat returnStdout: true, script: "sfdx force:package:version:create --package "MyDXPackage" --definitionfile config/project-scratch-def.json"
            printf rmsg
                
            rmsg = bat returnStdout: true, script: "sfdx force:package:install --package "MyDXPackage" --targetusername "biswajit3.d@tcs.com"
            printf rmsg    
        }

        //stage('Push To Test Org') {
            //rc = bat returnStatus: true, script: "sfdx force:source:push --targetusername ${SFDC_USERNAME}"
            //if (rc != 0) {
                //error 'push failed'
            //}
            // assign permset
            //rc = bat returnStatus: true, script: "sfdx force:user:permset:assign --targetusername ${SFDC_USERNAME}"
            //if (rc != 0) {
                //error 'permset:assign failed'
            //}
        //}

        //stage('collect results') {
          //  junit keepLongStdio: true, testResults: 'tests/**/*-junit.xml'
        //}
    }
}
