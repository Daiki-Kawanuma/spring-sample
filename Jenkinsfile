pipeline{
    agent{
        label 'master'
    }

    stages{
        stage('compile'){
            steps{            
				sh 'mvn clean compile'
            }
		}

		stage('unit test'){
		    steps{
		    	sh'echo "unit test"'
				sh 'mvn test'
				step([
					$class: 'JacocoPublisher',
				    classPattern: 'target/classes',
				    sourcePattern: 'src/main/java',
				    exclusionPattern: 'src/test*'
				])
				def checkstyle = scanForIssues tool: checkStyle(pattern: 'target/checkstyle-result.xml')
        		publishIssues issues: [checkstyle]

				def spotbugs = scanForIssues tool: spotBugs(pattern: 'target/spotbugsXml.xml')
        		publishIssues issues: [spotbugs]

				publishIssues id: 'analysis', name: 'All Issues', 
            		issues: [checkstyle, spotbugs], 
            		filters: [includePackage('io.jenkins.plugins.analysis.*')]
		    }		    
		}		
	}

    post {
	    failure {
			emailext (
				subject: "Your pipeline job was faild: ${currentBuild.fullDisplayName}",
				body: "FAILED: JENKINS Job [${currentBuild.fullDisplayName}] is failed. The branch is [${env.BRANCH_NAME}] Please go to ${env.BUILD_URL}.",
				recipientProviders: [[$class: 'DevelopersRecipientProvider']]
			)
	    }
    }
}
