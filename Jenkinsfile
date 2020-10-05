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
				step( [ $class: 'JacocoPublisher',
				      classPattern: 'target/classes',
				      sourcePattern: 'src/main/java',
				      exclusionPattern: 'src/test*'
				 ] )

		    }		    
		}

		stage('build and packaging'){
			steps{
			    sh 'mvn clean package -DskipTests'
			    sh """
			    mkdir -p app/target/
			    cp target/*.war app/
			    cp target/*.war app/target/
			    ls app/
			    ls app/target/
			    """
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