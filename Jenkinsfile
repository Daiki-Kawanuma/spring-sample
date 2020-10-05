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

				recordIssues tools: [java(), javaDoc(), 
            		checkStyle(), spotBugs(pattern: 'target/spotbugsXml.xml'),
            		taskScanner(highTags:'FIXME', normalTags:'TODO', includePattern: '**/*.java', excludePattern: 'target/**/*'), ]				

				step([
					$class: 'JacocoPublisher',
				    classPattern: 'target/classes',
				    sourcePattern: 'src/main/java',
				    exclusionPattern: 'src/test*'
				])
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
