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
			
			def tag = "${jarFileName}/${jarFileVersion}"
			sh "git tag -a ${tag} -m 'automatic jenkins tag 1231 Development MB AA'"
						
			withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: "b-liberman", usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD']]) {
     		   	def authenticatedUrl = git.authenticatedUrl(repositoryUrl, env.USERNAME, env.PASSWORD)
        		sh("git remote set-url origin ${authenticatedUrl} &> /dev/null")
        		sh("git push origin tag ${tag} &> /dev/null")
    		
    		}
		} else {
			echo "do nothing in any branch but master"
		}
    	
	}
}