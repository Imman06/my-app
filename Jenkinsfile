node{
   stage('SCM Checkout'){
     git 'https://github.com/Imman06/my-app'
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
  stage('Build Docker Image'){
   sh 'docker build -t imman06/myweb:0.0.2 .'
   }
   stage('Docker Image Push'){
   withCredentials([string(credentialsId: 'dockerPass', variable: 'dockerPassword')]) {
   sh "docker login -u imman06 -p ${dockerPassword}"
    }
   sh 'docker push imman06/myweb:0.0.2'
   }
   stage('Nexus Image Push'){
   sh "docker login -u admin -p admin123 13.233.91.162:8083"
   sh "docker tag imman06/myweb:0.0.2 13.233.91.162:8083/imman:1.0.0"
   sh 'docker push 13.233.91.162:8083/imman:1.0.0'
   }
stage('Remove Previous Container'){
	try{
		sh 'docker rm -f tomcattest'
	}catch(error){
		//  do nothing if there is an exception
	}   
}
stage('Docker deployment'){
   sh 'docker run -d -p 8090:8080 --name tomcattest imman06/myweb:0.0.2' 
   }
}
