pipeline {
  agent any
  tools {
    maven "maven3"
    jdk "Java8"
  }
  stages {
    stage("Clone the Repo") {
      steps {
        git branch: "nexusArtifactUploader",
          url: "https://github.com/AhmedOkila/Spring-Boot-Maven-Project.git",
          credentialsId: "Github"
      }
    }
    stage("Build and Deploy the Application") {
      steps {
        configFileProvider(
          [configFile(fileId: '74700203-7d44-4bbd-bb7c-030265d0723d', variable: 'MAVEN_SETTINGS')]) {
          sh 'mvn -s $MAVEN_SETTINGS clean deploy'
        }
      }
    }
  }
}