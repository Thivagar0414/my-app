node{
   stage('SCM Checkout'){
     git 'https://github.com/Thivagar0414/my-app.git'
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
   stage('Remove Previous Container'){
	try{
		sh 'docker rm -f tomcattest'
	}catch(error){
		//  do nothing if there is an exception
	}
   stage('Build Docker Imager'){
   sh 'docker build -t thivagad/myweb:4.1.4 .'
   }
   stage('Docker Image Push'){
   withCredentials([string(credentialsId: 'dockerPass', variable: 'dockerPassword')]) {
   sh "docker login -u thivagad -p ${dockerPassword}"
    }
   sh 'docker push thivagad/myweb:4.1.4'
   }
   stage('Nexus Image Push'){
   sh "docker login -u admin -p admin123 15.206.127.19:8083"
   sh "docker tag thivagad/myweb:4.1.4 15.206.127.19:8083/thiva:1.0.0"
   sh 'docker push 15.206.127.19:8083/thiva:1.0.0'
   }
   stage('Docker deployment'){
   sh 'docker run -d -p 8090:8080 --name tomcattest thivagad/myweb:4.1.4' 
   }
}
}
