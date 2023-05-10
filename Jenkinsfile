pipeline {
  agent any

  tools {
    maven 'M386'
  }

  stages {

    stage('Create Release Branch') {
      steps {
         def pom = readMavenPom file: 'pom.xml'
                    print "POM artifactId: " + pom.artifactId
                    print "POM version: " + pom.version
        echo "Starting Create Release Branch..."
        sh "git checkout -b 'release-${pom.version}'"
        // sh "mvn versions:set -DnewVersion='${env.BUILD_VERSION}'"
        sh "git branch"
        echo "Create Release Branch: ${currentBuild.currentResult}"
      }
      post {

        success {

          echo "...Create Release Branch Succeeded for 'release-${pom.version}': ${currentBuild.currentResult}"
        }
        unsuccessful {

          echo "...Create Release Branch Failed for 'release-${pom.version}': ${currentBuild.currentResult}"
        }
      }
    }

    stage('Build and Verify') {
      steps {

        sh 'mvn clean verify'

      }
    }

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

    stage('Push Release Branch') {

      steps {
        script {

          echo "Starting Push Release Branch..."
          sh "git add pom.xml"
          sh 'git commit -m "Committing Branch"'
          sh "git push --set-upstream origin 'release-${POM_VERSION}'"
          echo "Build Successful...branch 'release-${POM_VERSION}' committed"
        }

      }
      post {
        success {
          echo "...Push Release Branch Succeeded for 'release-${POM_VERSION}': ${currentBuild.currentResult}"
        }
        unsuccessful {
          echo "...Push Release Branch Failed for 'release-${POM_VERSION}': ${currentBuild.currentResult}"
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

    stage('Deploy to CH2') {
      steps {
        sh 'mvn clean deploy -DmuleDeploy'
      }
    }

  }
}