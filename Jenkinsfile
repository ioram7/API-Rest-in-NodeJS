def status = 'SUCCESS'

node {

    stage('Setup - Install dependencies') {

      env.NODEJS_HOME = "${tool 'NodeJS'}"
      env.PATH="${env.NODEJS_HOME}/bin:${env.PATH}"
    
      echo '######## (DooD) STARTING ########'
  
      try { 

        sh "sudo chown -R jenkins: ${WORKSPACE}"
        deleteDir()       
        
        checkout scm

        sh 'npm i -D jest@23.6.0 -E'
        sh 'sudo apt-get install -y nodejs'
        sh 'npm install -D jest jest-junit'
        sh "sudo printenv > result"
      
      } catch (e) {
          status = 'FAILED'
          echo 'Could not into the VM.'
          throw e
        }
      finally {  
      
        if (status=='SUCCESS'){ 
          
          sh 'echo "Finally something is working..."  >> result'
          stash includes: '**/result', name: 'res'
        }
        else{
          
          sh 'echo "Build failed.. Try again." >> result'
          archiveArtifacts artifacts: '**/result', fingerprint: true
        }
      }  
      echo '######## (DooD) FINISHED ########'
    }

    stage('Testing'){

      parallel FrontendTests: { 
                  echo 'Testing Frontend..'
                  try { 
                    sh 'npm test'
                  } catch (e) {
                    echo "tests failed"
                  }                  
                },
               BackendTests: { 
                  echo 'Testing Backend..' 
                }
    }

    stage('Deploy') {

      if (currentBuild.result == null || currentBuild.result == 'SUCCESS') {
            sh 'make publish'
      }
      else {
            sh 'make publish'
      }
    }

}
