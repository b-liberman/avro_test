node  {

	def scmVars

	stage('start') {
		echo "Starting pipeline triggered by git"
	}
	
	stage('clone repository') {
		scmVars = checkout scm
		scmVars.each { k, v -> echo "*** ${k}:${v}"}
	}
	
	jarFileName = 'avro_test'
	jarFileVersion = currentBuild.number
	
	stage('build project') {
		echo "./gradlew -PjarFileName=${jarFileName} clean build test"
    	sh "./gradlew -PjarFileName=${jarFileName} -PjarFileVersion=${jarFileVersion} clean build test"
	}
	
	stage('build docker image') {
    		app = docker.build("boris/avrotest:${currentBuild.number}", "--build-arg jarFileName=${jarFileName} --build-arg jarFileVersion=${jarFileVersion} .")
	}
	
	stage('set tag in GIT') {
	
		if(env.BRANCH_NAME == 'master') {
			sh "git tag ${jarFileName}/${jarFileVersion} -m 'automatic jenkins tag 1231 Development MB AA'"
    		sh "git push origin master"
		} else {
			echo "do nothing in any branch but master"
		}
    	
	}
}