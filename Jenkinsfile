#!groovy
2 import groovy.json.JsonSlurperClassic
3 node {
4
5     def BUILD_NUMBER=env.BUILD_NUMBER
6     def RUN_ARTIFACT_DIR="tests/${BUILD_NUMBER}"
7     def SFDC_USERNAME
8
9     def HUB_ORG=env.HUB_ORG_DH
10     def SFDC_HOST = env.SFDC_HOST_DH
11     def JWT_KEY_CRED_ID = env.JWT_CRED_ID_DH
12     def CONNECTED_APP_CONSUMER_KEY=env.CONNECTED_APP_CONSUMER_KEY_DH
13
14     println 'KEY IS'
15     println JWT_KEY_CRED_ID
16     println HUB_ORG
17     println SFDC_HOST
18     println CONNECTED_APP_CONSUMER_KEY
19     def toolbelt = tool 'toolbelt'
20
21     stage('checkout source') {
22         // when running in multi-branch job, one must issue this command
23         checkout scm
24     }
25
26     withCredentials([file(credentialsId: JWT_KEY_CRED_ID, variable: 'jwt_key_file')]) {
27         stage('Deploye Code') {
28             if (isUnix()) {
29                 rc = sh returnStatus: true, script: "${toolbelt} force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwtkeyfile ${jwt_key_file} --setdefaultdevhubusername --instanceurl ${SFDC_HOST}"
30             }else{
31                  rc = bat returnStatus: true, script: "\"${toolbelt}\" force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwtkeyfile \"${jwt_key_file}\" --setdefaultdevhubusername --instanceurl ${SFDC_HOST}"
32             }
33             if (rc != 0) { error 'hub org authorization failed' }
34
35 			println rc
36
37 			// need to pull out assigned username
38 			if (isUnix()) {
39 				rmsg = sh returnStdout: true, script: "${toolbelt} force:mdapi:deploy -d manifest/. -u ${HUB_ORG}"
40 			}else{
41 			   rmsg = bat returnStdout: true, script: "\"${toolbelt}\" force:mdapi:deploy -d manifest/. -u ${HUB_ORG}"
42 			}
43
44             printf rmsg
45             println('Hello from a Job DSL script!')
46             println(rmsg)
47         }
48     }
49 }