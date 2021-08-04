pipeline {
    agent any
    tools {
        maven "maven"
    }
	stages{
		
  stage("Curl token") {
            steps {
                script {

			def response = bat (script: 'curl -H "Content-Type: application/json" -X POST https://anypoint.mulesoft.com/accounts/api/v2/oauth2/token -d "{\\"client_id\\":\\"29c8bd2f493e434f82d3dc5a5a6166de\\",\\"client_secret\\":\\"b268b034B8E74E619Bc5c5507B834782\\",\\"grant_type\\":\\"client_credentials\\"}" | C:\\Users\\naimish_kakkad\\jq-win64.exe .\\"access_token\\"',returnStdout: true)
			echo response
			println(response.substring(response.indexOf("access_token")+14))
		}
            }
        }
		
		
	    
	    
         stage('Build') {
            steps {
               bat "mvn -B -U -e -V clean -DskipTests package"
            }
        }
        stage('Test') {
            steps {
             
               bat "mvn test -Dhttp.port=8093"
            }
        }
	    
	    
	    
        stage('Publish to Nexus Repository') {
			
            steps {
                script {
                    pom = readMavenPom file: "pom.xml";
                    filesByGlob = findFiles(glob: "target/*.jar");
                    echo "${filesByGlob[0].name} ${filesByGlob[0].path} ${filesByGlob[0].directory} ${filesByGlob[0].length} ${filesByGlob[0].lastModified}"
                    artifactPath = filesByGlob[0].path;
                    artifactExists = fileExists artifactPath;
			propertyFileByGlob = findFiles(glob: "*.properties");
			propertiesFile =  propertyFileByGlob[0].path;
                    if(artifactExists) {
                        echo "*** File: ${artifactPath}, group: ${pom.groupId}, packaging: ${pom.packaging}, version ${pom.version}";
                        nexusArtifactUploader(
                            nexusVersion: NEXUS_VERSION,
                            protocol: NEXUS_PROTOCOL,
                            nexusUrl: NEXUS_URL,
                            groupId: pom.groupId,
			    version: "${BUILD_NUMBER}",
                            repository: NEXUS_REPOSITORY,
                            credentialsId: "NEXUS_CREDENTIALS",
                            artifacts: [
                                // Artifact generated such as .jar, .ear and .war files.
                                [artifactId: pom.artifactId,
                                classifier: '',
                                file: artifactPath,
                                type: "jar"],
				// Lets upload the pom.xml file for additional information for Transitive dependencies
				[artifactId: pom.artifactId,
                                classifier: '',
                                file: "pom.xml",
                                type: "pom"],
                                // Lets upload the pom.xml file for additional information for Transitive dependencies
                                [artifactId: pom.artifactId,
                                classifier: '',
                                file: propertiesFile,
                                type: "properties"]
                            ]
                        );
                    } else {
                        error "*** File: ${artifactPath}, could not be found";
                    }
                }
            }
		}	
	}
	//post {  
       //  success {  
         //    mail bcc: '', body: "<b>Build Success</b><br>Project: ${env.JOB_NAME} <br>Build Number: ${env.BUILD_NUMBER} <br> URL: ${env.BUILD_URL}", cc: '', charset: 'UTF-8', from: '', mimeType: 'text/html', replyTo: '', subject: "Build Success: Project name -> ${env.JOB_NAME}", to: "naimish.mulesoft.architect@gmail.com";
        // }  
       //  failure {  
       //      mail bcc: '', body: "<b>Build Failure</b><br>Project: ${env.JOB_NAME} <br>Build Number: ${env.BUILD_NUMBER} <br> URL: ${env.BUILD_URL}", cc: '', charset: 'UTF-8', from: '', mimeType: 'text/html', replyTo: '', subject: "Build Failure: Project name -> ${env.JOB_NAME}", to: "naimish.mulesoft.architect@gmail.com";  
       //  }  
    // }  
}

