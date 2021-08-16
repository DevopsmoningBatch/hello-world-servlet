//node {
 
 // def mvnHome
   
//stage('Preparation') { 
// for display purposes

      // Get some code from a GitHub repository

   //   git 'https://github.com/raknas999/hello-world-servlet.git'

      // Get the Maven tool.
     
 // ** NOTE: This 'M3' Maven tool must be configured
 
     // **       in the global configuration.   
        
    //  mvnHome = tool 'Maven'
  // }

//   stage('Build') {
      // Run the maven build

   //   if (isUnix()) {
    //     sh "'${mvnHome}/bin/mvn' -Dmaven.test.failure.ignore clean package"
   //   } 
    //  else {
     //    bat(/"${mvnHome}\bin\mvn" -Dmaven.test.failure.ignore clean package/)
      
//}
 //  }
 
//  stage('Results') {
     // junit '**/target/surefire-reports/TEST-*.xml'
    //  archive 'target/*.war'
  
// }
//}

pipeline {
    agent any
options { buildDiscarder(logRotator(numToKeepStr: '5')) }
    stages {
        stage('Checkout from git') 
		{
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/develop']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/DevopsmoningBatch/hello-world-servlet.git']]])
            }
        }
		
		stage('Maven  build') 
		{
            steps {
               sh 'clean package cobertura:cobertura -Dcobertura.report.format=xml'
            }
        }
		//stage('Sonar quality') {
         //   steps {
          //     withSonarQubeEnv {
                // some block
         // }
          //  }
       // }
   stage('Sonarqube') {
   environment {
        scannerHome = tool 'sonarqube';
           
    }
    steps {
      withSonarQubeEnv('sonarqube') {
          
       sh "${scannerHome}/bin/sonar-scanner"
       }
             }
    }
		stage('Nexus upload') {
            steps {
                nexusPublisher nexusInstanceId: '1234', nexusRepositoryId: 'HelloWorldServlet', packages: [[$class: 'MavenPackage', mavenAssetList: [[classifier: '', extension: '', filePath: 'target/helloworld.war']], mavenCoordinate: [artifactId: 'hello-world-servlet-example', groupId: 'com.geekcap.vmturbo', packaging: 'war', version: '$BUILD_NUMBER']]]
            }
        }
		stage('Deploy to tomcat') {
            steps {
               deploy adapters: [tomcat8(path: '', url: 'http://13.59.157.215:8080/')], contextPath: 'HelloWorldServlet', war: '**/*.war'
            }
        }
		
    }
	post{
	success {
	cobertura autoUpdateHealth: false, autoUpdateStability: false, coberturaReportFile: '**/target/site/cobertura/coverage.xml', conditionalCoverageTargets: '70, 0, 0', failUnhealthy: false, failUnstable: false, lineCoverageTargets: '80, 0, 0', maxNumberOfBuilds: 0, methodCoverageTargets: '80, 0, 0', onlyStable: false, sourceEncoding: 'ASCII', zoomCoverageChart: false
	junit '**/target/surefire-reports/*.xml'
	}
	
	
	failure
	{
	mail to: "byra.pavi@gmail.com", body: "bild failed", subject: "call is failed"
	}
	}
}


