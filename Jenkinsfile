pipeline {
 agent any
 //agent {    
   // node { label 'maven' }         
 //}     
  
 //environment {
  //constants = load 'constants.groovy'
  //giturl = "${constants.giturl}"
// }
stages {
 
stage('git clone') {   
steps {
 
script {
def readContent = readFile env.WORKSPACE+"/test.txt"
 sh "echo ${readContent/namespace}"
 //def scmUrl = scm.getUserRemoteConfigs()[0].getUrl()
 // def constants = load 'constants.groovy'
//  def giturl = constants.giturl
  //sh "echo ${env.GIT_URL}"
// sh "echo ${env.absoluteUrl}"
 //sh "echo ${env.GIT_COMMIT}"
}
// git url: "${giturl}"
 //}
  //git url: "${giturl}"
// git url: 'https://github.com/shweta9651/simple-java-project.git'
// git url: 'https://github.com/miguno/java-docker-build-tutorial.git'  

}
}  


     
 }
}        
 
