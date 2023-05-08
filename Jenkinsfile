pipeline {
  agent any

  tools {
    maven 'M386'
  }

	
  stages {
  
  
  /* 
    stage('SonarQube Analysis') {
      steps {
        withSonarQubeEnv('SonarQube22') {
        sh 'mvn clean verify sonar:sonar  -Dsonar.projectKey=test  -Dsonar.host.url=http://localhost:9000 -Dsonar.login=sqp_e0d26f9c0ae239f417fd7357042003f6fdd2d48c -Dsonar.sources=src/'
      } 
    }
    }

    stage('SonarQube Quality Gate') {
      steps {
        withSonarQubeEnv('SonarQube22') {
          timeout(time: 2, unit: 'MINUTES') {
             script {
               waitForQualityGate abortPipeline: true
             }
         }
       }
      } 
    }

    stage('Push Artefact to Exchange') {
      steps {
        sh 'mvn clean deploy -Pexchange'
      }
    }
	
	    stage('Push Artefact to Nexus') {
      steps {
        sh 'mvn clean deploy -Pnexus-snapshot'
      }
    }
	*/
	stage('Deploy to CH2') {
      steps {
        sh 'mvn clean deploy -DmuleDeploy -X'
      }
    }

  }
}