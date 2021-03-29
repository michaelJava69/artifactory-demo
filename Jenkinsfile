pipeline {
  agent any 

  parameters {
         
        choice(name: 'SERVER_ID', 
          choices: 'artifactory\nlocal',
          description: 'What artifacts repository?')
         
          
    }


  stages{
   stage('Build') {
       steps {
         script {
          dir("test")
            {
             sh  'touch $WORKSPACE/Artifact_$BUILD_NUMBER'
             sh 'echo "SERVER_ID is ${SERVER_ID}"'
            }
            }
          }
        }
         stage ('Upload') {
            steps {
              
              rtServer (
                  id: 'artifactory-server',
                  url: 'http://localhost:8082/artifactory',
                  // If you're using username and password:
                  // username: 'xxxx',
                  // password: 'xxxxxxx',
                  // If you're using Credentials ID:
                  credentialsId: 'articatory-id',
                  // If Jenkins is configured to use an http proxy, you can bypass the proxy when using this Artifactory server:
                  // bypassProxy: true,
                  // Configure the connection timeout (in seconds).
                  // The default value (if not configured) is 300 seconds:
                  // timeout: 300
                )
                rtUpload (
                    buildName: JOB_NAME,
                    buildNumber: BUILD_NUMBER,
                    serverId: 'artifactory-server', // Obtain an Artifactory server instance, defined in Jenkins --> Manage:
                    spec: '''{
                              "files": [
                                 {
                                  "pattern": artifactory-serverArtifact_*",
                                  "target": "result/",
                                  "recursive": "false"
                                } 
                             ]
                        }'''    
                    )
            }
        }
        stage ('Publish build info') {
            steps {
               // rtPublishBuildInfo (
               //     buildName: JOB_NAME,
               //     buildNumber: BUILD_NUMBER,
               //     serverId: "${SERVER_ID}"
               // )
               rtServer (
                  id: 'artifactory-server',
                  url: 'http://localhost:8082/artifactory',
                  // If you're using username and password:
                  // username: 'xxxx',
                  // password: 'xxxxxxx',
                  // If you're using Credentials ID:
                  credentialsId: 'artifactory-id',
                  // If Jenkins is configured to use an http proxy, you can bypass the proxy when using this Artifactory server:
                  // bypassProxy: true,
                  // Configure the connection timeout (in seconds).
                  // The default value (if not configured) is 300 seconds:
                  // timeout: 300
                )
                rtPublishBuildInfo (
                    buildName: JOB_NAME,
                    buildNumber: BUILD_NUMBER,
                    serverId: 'artifactory-server'
                )
            }
        }
         stage ('Add interactive promotion') {
            steps {
                rtServer (
                  id: 'artifactory-server',
                  url: 'http://localhost:8082/artifactory',
                  // If you're using username and password:
                  // username: 'xxxx',
                  // password: 'xxxxxxx',
                  // If you're using Credentials ID:
                  credentialsId: 'artifactory-id',
                  // If Jenkins is configured to use an http proxy, you can bypass the proxy when using this Artifactory server:
                  // bypassProxy: true,
                  // Configure the connection timeout (in seconds).
                  // The default value (if not configured) is 300 seconds:
                  // timeout: 300
                )
                rtAddInteractivePromotion (
                    //Mandatory parameter
                    serverId: 'artifactory-server',

                    //Optional parameters
                    targetRepo: 'result/',
                    displayName: 'Promote me please',
                    buildName: JOB_NAME,
                    buildNumber: BUILD_NUMBER,
                    comment: 'this is the promotion comment',
                    sourceRepo: 'result/',
                    status: 'Released',
                    includeDependencies: true,
                    failFast: true,
                    copy: true
                )

                rtAddInteractivePromotion (
                    serverId: 'artifactory-server',
                    buildName: JOB_NAME,
                    buildNumber: BUILD_NUMBER
                )
            }
         }
        stage ('Removing files') {
            steps {
                sh 'rm -rf $WORKSPACE/*'
            }
        }
         
        
  }
}
