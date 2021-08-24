pipeline {
    agent any 
    tools { 
        maven 'Maven' 
      
    }
stages { 
     
 stage('Preparation') { 
     steps {
// for display purposes

      // Get some code from a GitHub repository

      git 'https://github.com/DevopsmoningBatch/hello-world-servlet.git'
         //git 'https://github.com/raknas999/hello-world-servlet.git'

      // Get the Maven tool.
     
 // ** NOTE: This 'M3' Maven tool must be configured
 
     // **       in the global configuration.   
     }
   }

   stage('Build') {
       steps {
       // Run the maven build

      //if (isUnix()) {
         sh 'mvn -Dmaven.test.failure.ignore=true install'
      //} 
      //else {
      //   bat(/"${mvnHome}\bin\mvn" -Dmaven.test.failure.ignore clean package/)
       }
//}
   }
 
  stage('Results') {
      steps {
      junit '**/target/surefire-reports/TEST-*.xml'
      archiveArtifacts 'target/*.war'
      }
 }
 stage('Sonarqube') {
   environment {
      scannerHome = tool 'sonarqube'
  }
   steps {
     withSonarQubeEnv('sonarqube') {
          sh "${scannerHome}/bin/sonar-scanner"
     }
       // timeout(time: 10, unit: 'MINUTES') {
         // waitForQualityGate abortPipeline: true
     // }
  }
}
     stage('Artifact upload') {
      steps {
     nexusPublisher nexusInstanceId: '1234', nexusRepositoryId: 'releases', packages: [[$class: 'MavenPackage', mavenAssetList: [[classifier: '', extension: '', filePath: 'target/helloworld.war']], mavenCoordinate: [artifactId: 'hello-world-servlet-example', groupId: 'com.geekcap.vmturbo', packaging: 'war', version: '$BUILD_NUMBER']]]
      }
 }
     stage('Deploy War') {
     // steps {
    //    sh label: '', script: 'ansible-playbook deploy.yml'
     // }
         steps {
               deploy adapters: [tomcat8(path: '', url: 'http://3.137.136.151:8080/')], contextPath: 'HelloWorldServlet', war: '**/*.war'
            }
 }
}
post {
        success {
            mail to:"byra.pavi@gmail.com", subject:"SUCCESS: ${currentBuild.fullDisplayName}", body: "Build success"
        }
        failure {
            mail to:"byra.pavi@gmail.com", subject:"FAILURE: ${currentBuild.fullDisplayName}", body: "Build failed"
        }
    }       
}
