pipeline {        
  tools {
     maven 'mvn'
     }
  agent any
  environment {
        giturl = 'https://58966bd9be77345db03ef4380fab2ce5e4c11a7b@github.ibm.com/demo-microservice/cache-manager'
        projectKey = 'cachemanager'
        imageBuild = 'cache-manager'
        projectName = 'estore'
  }  
  stages {
    stage('Build App') {
  
     steps {
       git url: env.giturl
       script{      
       sh "mvn install -DskipTests=true"
       }
      }
    }
    stage('Test') {

      steps {
       script{      
           sh "mvn test"
         }
        step([$class: 'JUnitResultArchiver', testResults: '**/target/surefire-reports/TEST-*.xml'])
      }  
    }
    stage('Code Analysis') {
      environment {
        scannerHome = tool 'sonarscanner'
      
    }    

      steps {
        withSonarQubeEnv('sonar') {    
         sh '${scannerHome}/bin/sonar-scanner -X -Dsonar.projectKey=env.projectKey -Dsonar.sources=src -Dsonar.java.binaries=. -DskipTests=true'
  }
      }
    }
    stage('Create Image Builder') {
      steps {
        script {
          openshift.withCluster() {
              openshift.withProject("${env.projectName}") {
              def bc = openshift.selector("bc", "${env.imageBuild}")
                  if (true == true) {
                  echo "Creating a new Build"
                  openshift.newBuild("--name=${env.imageBuild}", "--strategy=docker", "--allow-missing-imagestream-tags=true", "--binary=true")
                  }
              }
          }
         }
      }
    }
    stage('Build Image') {

      steps {
        script {
          openshift.withCluster() {
            openshift.withProject("${env.projectName}") {
             if (true == true) {
              openshift.selector("bc", "${env.imageBuild}").startBuild("--from-dir=.")
              }
            }
          }
        }
       }
    }
    stage('Tag Image') {
      steps {
        script {
          openshift.withCluster() {
          openshift.withProject("${env.projectName}"){
          openshift.tag("${env.imageBuild}", "${env.imageBuild}:dev")
          openshift.tag("${env.imageBuild}", "${env.imageBuild}:promoteToQA")
         }
       }
     }
    }
   }
    stage('Deploy In Dev') {
          steps {
            script {
              openshift.withCluster() {
                openshift.withProject("${env.projectName}") {
                  def app
                  def params=""
                  if (false ==true){
                   params="-e test"
                  } 
                  if (true == true) {
                  echo "Creating the new app"
                  app = openshift.newApp("estore/${env.imageBuild}","--allow-missing-imagestream-tags=true", "-e PROFILE=sandbox","-e LOG_PATH=/tmp/logs",params)
                  }else{
                  echo "USing image"
                  app = openshift.newApp("codecentric/springboot-maven3-centos~${env.giturl} --name=${env.imageBuild}",params)
                  def bc = app.narrow("bc")
                  echo "Finding the builds"                     
                  def builds = bc.related('builds')
                  echo "Found the builds"                     
                  timeout(18) {
                      builds.watch {
                        return it.count() > 0
                      }
                      builds.watch {
                        if ( it.count() == 0 ) return false
                        def allDone = true
                        it.withEach{
                            def buildModel = it.object()
                        if ( it.object().status.phase != "Complete" ) {
                           allDone = false
                           }
                        }

                          return allDone;
                        } 
                        builds.untilEach(1) { 
                          return it.object().status.phase == "Complete"
                        }
                       }
                  }     
                  app.narrow("svc").expose();
                  def dc = app.narrow("dc")
                  openshiftScale(namespace: "${env.projectName}",depCfg: "${env.imageBuild}", replicaCount: 2)
                  openshift.set("triggers", "dc/${env.imageBuild}","--auto")
                  if(true == true){ 
                     dc = openshift.selector("dc", "${env.imageBuild}")
                     def dcmap = dc.object()
                     dcmap.spec.template.metadata.annotations.put("sidecar.istio.io/inject", "true")
                     openshift.apply(dcmap)
                     minReplicas: 1
                     maxReplicas: 2 
                  }  
                }
              }
            }
          }
    }
    }
  }
