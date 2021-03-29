pipeline {
  agent any 

  parameters {
         
        choice(name: 'SERVER_URL', 
          choices: 'http://localhost:8082/artifactory\nhttp://localhost:8082/artifactory',
          description: 'What artifacts repository?')
         
          
    }


  stages{
   stage('Build') {
       steps {
         script {
          dir("test")
            {
             sh  'touch $WORKSPACE/Artifact_$BUILD_NUMBER'
             sh 'echo "SERVER_URL is ${SERVER_URL}"'
            }
            }
          }
        }
         stage ('Upload') {
            steps {
              rtServer (
                    id: "ARTIFACTORY_SERVER",
                    url: "${SERVER_URL}" , 
                    // If you're using username and password:
                    //username: 'michael',
                    //password: 'Azuk@123',
                    //url: SERVER_URL,
                    credentialsId: 'articatory-id'
                )
               
                rtUpload (
                    buildName: JOB_NAME,
                    buildNumber: BUILD_NUMBER,
                    serverId: "ARTIFACTORY_SERVER", // Obtain an Artifactory server instance, defined in Jenkins --> Manage:
                    spec: '''{
                              "files": [
                                 {
                                  "pattern": artifactory-serverArtifact_*",
                                  "target": "result/",
                                  "recursive": "false"
                                } 
                             ]
                        }''',    
                    )
            }
        }
 
        
  }
}
