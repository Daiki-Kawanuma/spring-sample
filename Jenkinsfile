pipeline{
    agent{
        label 'master'
    }

    stages{
        stage('Build'){
            steps{            
				sh 'mvn -V -e clean compile'
            }
		}

		stage('Analysis'){
		    steps{		    	
				sh 'mvn test'

				junit testResults: 'target/*-reports/TEST-*.xml'

				recordIssues tools: [java(), javaDoc(), 
            		checkStyle(), spotBugs(pattern: 'target/spotbugsXml.xml')]				
		    }		    
		}

		stage ('Coverage') {        	
        	publishCoverage adapters: [jacocoAdapter('target/site/jacoco/jacoco.xml')]
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
