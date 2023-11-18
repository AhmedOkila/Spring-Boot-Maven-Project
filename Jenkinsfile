pipeline {
  agent any
  tools {
    maven "maven3"
    jdk "Java8"
  }
  environment {
    NEXUS_VERSION = "nexus3"
    NEXUS_PROTOCOL = "http"
    NEXUS_URL = "localhost:8081"
    NEXUS_REPOSITORY = "SpringBootMavenApp"
    NEXUS_CREDENTIAL_ID = "Nexus"
    ARTIFACT_VERSION = "${BUILD_NUMBER}"
  }
  stages {
    stage("Clone the Repo") {
      steps {
        git branch: "main",
          url: "https://github.com/AhmedOkila/Spring-Boot-Maven-Project.git",
          credentialsId: "Github"
      }
    }
    stage("Build the Application") {
      steps {
        sh "mvn clean package"
        sh "echo $BUILD_NUMBER"
      }
    }
    stage("Publish to nexus") {
      steps {
        script {
          // Read POM xml file using 'readMavenPom' step , this step 'readMavenPom' is included in: https://plugins.jenkins.io/pipeline-utility-steps
          pom = readMavenPom file: "pom.xml";
          // Find built artifact under target folder
          filesByGlob = findFiles(glob: "target/*.${pom.packaging}");
          // Print some info from the artifact found
          echo "${filesByGlob[0].name} ${filesByGlob[0].path} ${filesByGlob[0].directory} ${filesByGlob[0].length} ${filesByGlob[0].lastModified}"
          // Extract the path from the File found
          artifactPath = filesByGlob[0].path;
          // Assign to a boolean response verifying If the artifact name exists
          artifactExists = fileExists artifactPath;

          if (artifactExists) {
            echo "*** File: ${artifactPath}, group: ${pom.groupId}, packaging: ${pom.packaging}, version ${pom.version}";

            nexusArtifactUploader(
              nexusVersion: NEXUS_VERSION,
              protocol: NEXUS_PROTOCOL,
              nexusUrl: NEXUS_URL,
              groupId: pom.groupId,
              version: ARTIFACT_VERSION,
              repository: NEXUS_REPOSITORY,
              credentialsId: NEXUS_CREDENTIAL_ID,
              artifacts: [
                // Artifact generated such as .jar, .ear and .war files.
                [artifactId: pom.artifactId, //! This is the Artifact Name
                  classifier: '',
                  file: artifactPath,
                  type: pom.packaging
                ]
              ]
            );

          } else {
            error "*** File: ${artifactPath}, could not be found";
          }
        }
      }
    }
  }
}