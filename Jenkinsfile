node{
    
    stage('Clone repo'){
        git credentialsId: 'GIT-Credentials', url: 'https://github.com/bhanuprakash678910/mavenwebapp.git'
    }
    
    stage('Maven Build'){
        def mavenHome = tool name: "Maven-3.8.6", type: "maven"
        def mavenCMD = "${mavenHome}/bin/mvn"
        sh "${mavenCMD} clean package"
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
		groupId: 'in.bhanuit',
		nexusUrl: '',
		protocol: 'http',
		repository: 'bhanuit-release'
		version: '1.0.0'
	}
}
    
    stage('Build Image'){
        sh 'docker build -t bhanuit/mavenwebapp .'
    }
    
    stage('Push Image'){
        withCredentials([string(credentialsId: 'DOCKER-CREDENTIALS', variable: 'DOCKER_CREDENTIALS')]) {
            sh 'docker login -u bhanuit -p ${DOCKER_CREDENTIALS}'
        }
        sh 'docker push bhanuit/mavenwebapp'
    }
    
    stage('Deploy App'){
        kubernetesDeploy(
            configs: 'maven-web-app-deploy.yml',
            kubeconfigId: 'Kube-Config'
        )
    }    
}
