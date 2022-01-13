node{
   stage('SCM Checkout'){
     git 'https://github.com/sureshkanneboina/my-app'
   }
   stage('Compile-Package'){

      def mvnHome =  tool name: 'maven3', type: 'maven'   
      sh "${mvnHome}/bin/mvn clean package"
	  sh 'mv target/myweb*.war target/newapp.war'
   }
   stage('SonarQube Analysis') {
	        def mvnHome =  tool name: 'maven3', type: 'maven'
	        withSonarQubeEnv('sonar') { 
	          sh "${mvnHome}/bin/mvn sonar:sonar"
	        }
	    }
   stage('Build Docker Imager'){
   sh 'docker build -t sureshkanneboina/myweb:0.0.2 .'
   }
   stage('Docker Image Push'){
   withCredentials([string(credentialsId: 'dockerPass', variable: 'dockerPassword')]) {
   sh "docker login -u sureshkanneboina -p ${dockerPassword}"
    }
   sh 'docker push sureshkanneboina/myweb:0.0.2'
   }
   stage('Nexus Image Push'){
   sh "docker login -u admin -p admin123 13.235.24.172:8085"
   sh "docker tag sureshkanneboina/myweb:0.0.2 13.235.24.172:8085/suresh:1.0.0"
   sh 'docker push 13.235.24.172:8085/suresh:1.0.0'
   }
   stage('Remove Previous Container'){
	try{
		sh 'docker rm -f tomcattest'
	}catch(error){
		//  do nothing if there is an exception
	}
  stage('Docker deployment'){
   sh 'docker run -d -p 8090:8080 --name tomcattest sureshkanneboina/myweb:0.0.2' 
   }
 }
}
