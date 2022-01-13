#!groovy

pipeline
{
	agent any

	options 
	{
          	buildDiscarder(logRotator(numToKeepStr: '20'))
		timestamps()
     	}

	parameters
	{
		booleanParam(name: 'CLEAN_WORKSPACE', defaultValue: false, description: 'Delete folder created by Jenkins for current build if true')
		booleanParam(name: 'TESTING_FRONTEND', defaultValue: false , description: 'If true- execute front-end testing')
		
	}

	environment
	{
		 ON_SUCCESS_SEND_EMAIL = 'true'
		 ON_FAILURE_SEND_EMAIL = 'true'
	}
	
	stages()
	{
		stage("Build")
		{	
			steps
			{
				echo "Build number ${BUILD_NUMBER} with tag ${BUILD_TAG}"
					
				sh 'python --version && \
				python -m venv "${BUILD_TAG}" && \
                            . ${BUILD_TAG}/Scripts/activate && \
                              python -m pip install Pillow && \
                                 ${BUILD_TAG}/Scripts/pip install -r requirements.txt && \
                        python manage.py makemigrations && python manage.py migrate && \
                               python -m py_compile manage.py && \
                            deactivate'
				
			}
		}
		
		stage("Test back-end")
		{
			steps
			{
				sh '. ${BUILD_TAG}/Scripts/activate && py.test --junitxml=C:/ProgramData/Jenkins/.jenkins/workspace/Lab3-TIDPP/test-reports/test-report.xml && deactivate'
					
			}	
		}

		stage("Test front-end")
		{
			steps
			{
				script
				{
					if(params.TESTING_FRONTEND == 'true')
					{
						echo "Valoarea parametrului TESTING_FRONTEND este ${params.TESTING_FRONTEND}"
					}			
				}
			}
		}
		
		stage("Deployment")
		{
			steps
			{
				echo "Deployment step"
			}
			
		}

		
	}

	post
	{
		always
		{
				
			junit '**/test-reports/*.xml'

			script
			{
				if(params.CLEAN_WORKSPACE)
				{
					cleanWs()
				}
			}
		}
		
		success
		{
			script
			{
				if(env.ON_SUCCESS_SEND_EMAIL)
				{
					echo "Sending email with job name: ${JOB_NAME}, build number: ${BUILD_NUMBER}, build url: ${BUILD_URL}"
					emailext(body: "Success build! job name: ${JOB_NAME}, build number: ${BUILD_NUMBER}, build url: ${BUILD_URL}", subject: 'Jenkins success build ${BUILD_NUMBER}', to: 'victorcovrig464@gmail.com')
					echo "Email send: build success"
				}
			}
		}

		failure
		{
			script
			{
				if(env.ON_FAILURE_SEND_EMAIL)
				{
					echo "Sending email with job name: ${JOB_NAME}, build number: ${BUILD_NUMBER}, build url: ${BUILD_URL}"
					emailext(body: "Build failed. Please fix it! job name: ${JOB_NAME}, build number: ${BUILD_NUMBER}, build url: ${BUILD_URL}", subject: 'Jenkins failed build ${BUILD_NUMBER}', to: 'victorcovrig464@gmail.com')
					echo "Email send: build failure"
				}
			}
			
		}	
	}	
}