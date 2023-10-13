node{
    
    stage('Clone repo'){
        git credentialsId: 'github', url: 'https://github.com/ramki46/maven-web-app.git'
    }
    
    stage('Build and Test') {
      steps {
        sh 'mvn clean package'
    }
    
    stage('SonarQube analysis') {       
        withSonarQubeEnv('Sonar-Server-7.8') {
       	sh "mvn sonar:sonar"    	
    }
        
    stage('upload war to nexus'){
	steps{
		nexusArtifactUploader artifacts: [	
			[
				artifactId: '01-maven-web-app',
				classifier: '',
				file: 'target/01-maven-web-app.war',
				type: war		
			]	
		],
		credentialsId: 'nexus3',
		groupId: 'in.ashokit',
		nexusUrl: '',
		protocol: 'http',
		repository: 'ashokit-release'
		version: '1.0.0'
	}
}
    
    stage('Build Image'){
        sh 'docker build -t ramki46/mavenwebapp .'
    }
    
    stage('Push Image'){
        withCredentials([string(credentialsId: 'DOCKER-CREDENTIALS', variable: 'DOCKER_CREDENTIALS')]) {
            sh 'docker login -u ramki46 -p ${DOCKER_CREDENTIALS}'
        }
        sh 'docker push ramki46/mavenwebapp'
    }
    
    stage('Deploy App'){
        kubernetesDeploy(
            configs: 'maven-web-app-deploy.yml',
            kubeconfigId: 'Kube-Config'
        )
     }    
   }
 }
}
