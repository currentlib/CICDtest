pipeline {
	agent any
	environment {
		MAJOR = '1'
		MINOR = '0'
		UIPATH_ORCH_URL = "https://cloud.uipath.com/persoxnucynp/DefaultTenant/orchestrator_/"
		UIPATH_ORCH_LOGICAL_NAME = "CICDtest"
		UIPATH_ORCH_TENANT_NAME = "DefaultTenant"
		UIPATH_ORCH_FOLDER_NAME_DEV = "Dev"
		UIPATH_ORCH_FOLDER_NAME_PRD = "Prod"
	}
	stages {
		stage('Preparing') {
			steps {
				echo "Jenkins Home ${env.JENKINS_HOME}"
                		echo "Jenkins URL ${env.JENKINS_URL}"
                		echo "Jenkins JOB Number ${env.BUILD_NUMBER}"
                		echo "Jenkins JOB Name ${env.JOB_NAME}"
                		echo "GitHub BranhName ${env.BRANCH_NAME}"
                		checkout scm
			}
		}
		stage('Build') {
			steps {
				echo "Building..with ${WORKSPACE}"
				UiPathPack (
					outputPath: "Output\\${env.BUILD_NUMBER}",
					projectJsonPath: "project.json",
					version: [$class: 'ManualVersionEntry', version: "${MAJOR}.${MINOR}.${env.BUILD_NUMBER}"],
					useOrchestrator: false
				)
			}
		}
		stage('Test') {
			steps {
				echo 'Testing..the workflow...'
			}
		}
		stage('Deploy to Prod') {
			steps {
				echo "Deploying ${BRANCH_NAME} to Prod folder"
				UiPathDeploy(
					packagePath: "Output\\${env.BUILD_NUMBER}",
					orchestratorAddress: "${UIPATH_ORCH_URL}",
					orchestratorTenant: "${UIPATH_ORCH_TENANT_NAME}",
					folderName: "${UIPATH_ORCH_FOLDER_NAME}",
					environments: 'DEV',
					credentials: Token(accountName: "${UIPATH_ORCH_LOGICAL_NAME}", credentialsId: '7a59289b-3fd8-4b2a-b6d0-377de8113f5d'),
				)
			}
		}
	}
	post {
		success {
			echo 'Deployment has been completed!'
		}
		failure {
			echo "FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.JOB_DISPLAY_URL})"
		}
		always {
			cleanWs()
		}
	}
}