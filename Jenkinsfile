node {
    // reference to maven
    // ** NOTE: This 'maven-3.6.1' Maven tool must be configured in the Jenkins Global Configuration.   
    def mvnHome = tool 'maven'

    // holds reference to docker image
    def dockerImage
    // ip address of the docker private repository(nexus)
    
    def dockerRepoUrl = "l541024090925.dkr.ecr.us-east-2.amazonaws.com/"
    def dockerImageName = "springboot"
    def dockerImageTag = "${dockerRepoUrl}/${dockerImageName}:${env.BUILD_NUMBER}"
    
    stage('Clone Repo') { // for display purposes
      // Get some code from a GitHub repository
      git 'https://github.com/eby-sebastian/docker-hello-world-spring-boot.git'
      // Get the Maven tool.
      // ** NOTE: This 'maven-3.6.1' Maven tool must be configured
      // **       in the global configuration.           
      mvnHome = tool 'maven'
    }    
  
    stage('Build Project') {
      // build project via maven
      sh "'${mvnHome}/bin/mvn' -Dmaven.test.failure.ignore clean package"
    }
	
	stage('Publish Tests Results'){
      parallel(
        publishJunitTestsResultsToJenkins: {
          echo "Publish junit Tests Results"
		  junit '**/target/surefire-reports/TEST-*.xml'
		  archive 'target/*.jar'
        },
        publishJunitTestsResultsToSonar: {
          echo "This is branch b"
      })
    }
		
    stage('Build Docker Image') {
      // build docker image
      sh "whoami"
      sh "ls -all /var/run/docker.sock"
      sh "mv ./target/hello*.jar ./data" 
      
      dockerImage = docker.build("springboot")
    }
   
    stage('Deploy Docker Image'){
      
      // deploy docker image to nexus

      echo "Docker Image Tag Name: ${dockerImageTag}"
//withCredentials([usernamePassword(credentialsId: 'ecr', passwordVariable: 'pass', usernameVariable: 'user')]) {
       
      docker.withRegistry("https://541024090925.dkr.ecr.us-east-2.amazonaws.com", "ecr:us-east-2:aws") {
	      
      def customImage = docker.build("hello-world-java:${env.BUILD_ID}")
	      customImage.push()

     // docker.image("${dockerImageTag}").push()
     // sh "docker login -u admin -p admin123 ${dockerRepoUrl}"
     // sh "docker tag ${dockerImageName} ${dockerImageTag}"
     // sh "docker push ${dockerImageTag}"
    }
}
