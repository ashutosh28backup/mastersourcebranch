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
    //def pmd1=env.pmd
	
	
    println 'KEY IS' 
    println JWT_KEY_CRED_ID
    println HUB_ORG
    println SFDC_HOST
    println CONNECTED_APP_CONSUMER_KEY
    def toolbelt = tool 'toolbelt'
    println toolbelt
    def pmd = tool 'pmd'
	println pmd

//// test
	Date date= new Date();
	//timestamp= bat "echo %date%%time%"
	def timestamp=date.getTime()
	def filename = "C:\\Users\\Administrator\\Downloads\\mastersourcebranch-master\\mastersourcebranch-master\\force-app\\main\\pmdreport"
	filename = "\"" + filename + timestamp + ".html" + "\""
	println(filename)
	//println (timestamp)
// test ends
	
	
    cleanWs()
	
    stage('checkout source') {
        // when running in multi-branch job, one must issue this command
        checkout scm
    }
	
    stage('pmd') {  
	//pmdrun = bat returnStatus: true, script: "\"${pmd}\" -d "force-app\main\default\classes" -f html -R "category/apex/design.xml" -reportfile "force-app\output.html""
    pmdrun = bat returnStatus: true, script: "\"${pmd}\" -d \"C:\\Users\\Administrator\\Downloads\\mastersourcebranch-master\\mastersourcebranch-master\\force-app\\main\\default\\classes\" -f html -R \"category/apex/design.xml\" -reportfile ${filename}"
    println pmdrun
	    if(pmdrun!=0) {error 'analysis failed'}
	    else{println('analysis successful')}
    
    }
	
//	Authorizing SFDX for the environment	
	withCredentials([file(credentialsId: JWT_KEY_CRED_ID, variable: 'jwt_key_file')]) {
        stage('Authorization') {
                 rc = bat returnStatus: true, script: "\"${toolbelt}\" force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwtkeyfile \"${jwt_key_file}\" --setdefaultdevhubusername --instanceurl ${SFDC_HOST}"
            
            if (rc != 0) { error 'hub org authorization failed' }
		else{ println('Environment Authorized successfully')}
	}
	}

	//	Static code analysis		
	//stage('Static Code Analysis') {
	//SCA=bat returnStatus: true, script: "\"${toolbelt}\" scanner:run --target=.\\force-app --outfile=sfdxscanner1.html --format=html"
	//if (SCA != 0) { error 'Issues found in code scan' }
	//else{ println ('No major issues found in code scan') }
	
	//}
	
	
		
//	deployment validation running on Windows machine
	stage('deployment validation') {
	dv = bat returnStatus: true, script: "\"${toolbelt}\" force:source:deploy -u ${HUB_ORG} -m ApexClass -l RunAllTestsInOrg -c"
	if (dv != 0) { error 'Deployment Validation failed' }
	else{
		println ('Deployment validtaion succeeded')}

	}

		
		
// 	Doing deployment from Windows machine
	stage('deployment to Environment') {

	rmsg = bat returnStatus: true, script: "\"${toolbelt}\" force:source:deploy -u ${HUB_ORG} -x manifest/package.xml -w 60"
	if (rmsg != 0) { error 'Deployment Validation failed' }
	else{
		println ('Deployment succeeded')}
			}
        }

