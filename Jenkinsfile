node('docker'){
	stage('Poll'){
		scm checkout
	}
	stage('Build & Unit test'){
		sh 'mvn clean verify -DskipITs=true';
		junit '**/target/surefire-reports/TEST-*.xml'
		archive 'target/*.jar'
	}
	stage('Static Code Analysis'){
		sh'mvn clean verify sonar:sonar -Dsonar.projectName=example-project -Dsonar.projectkey=example-project -Dsonar.projectVersion=$Build_NUMBER';
	}
	stage('Intergration Test'){
		sh 'mvn clean verify -Dsurefire.skip=true';
		junit '**/target/failsafe-reports/TEST-*.xml'
		archive 'target/*.jar'
	}
	stage('Publish'){
		def server = Artifactory.server 'default artifactory server'
		def uploadSpec = """{
		"files":[
			{
				"pattern" : "[Mandatory]",
				"target" : "[Mandatory]",
				"props" : "[Optional]",
				"recursive" : "[Optional, Default: 'true']",
				"flat" : "[Optional, Default: 'true']",
				"regexp" : "[Optional, Default: 'false']"
			}
		]
		}"""
		server.upload(uploadSpec)
	}
}
