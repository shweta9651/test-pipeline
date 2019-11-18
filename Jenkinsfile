pipeline {
 agent { 
    node { label 'maven' }      
 }  
   
stages {
  
stage('git clone') {   
steps {
def constants = load 'constants.groovy'
def giturl = constants.giturl
 //git url: 'https://github.com/shweta9651/java-junit-sample.git'
// git url: 'https://github.com/miguno/java-docker-build-tutorial.git'  
git url: ${giturl}
}
}  

stage('compile source code') {
     steps {
         sh 'mvn clean install'
   
     }
  }
    
 }
}   
 
