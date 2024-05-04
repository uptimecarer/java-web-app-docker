node{
    
stage('checkout'){
    git credentialsId: '17d90143-b0c1-40b0-bb65-bbbb7a1a2e5f', url: 'https://github.com/hareesh244/java-web-app-docker.git'
}
stage(" Maven Clean Package"){
      def mavenHome =  tool name: "maven-3.9.6", type: "maven"
      def mavenCMD = "${mavenHome}/bin/mvn"
      sh "${mavenCMD} clean package"
      
    } 
stage('Build Docker Image'){
        sh 'docker build -t uptimecareer/java-webapp-docker .'
    }
stage('Push Docker Image'){
        withCredentials([string(credentialsId: 'Docker_Hub_Pwd', variable: 'Docker_Hub_Pwd')]) {
          sh "docker login -u uptimecareer -p ${Docker_Hub_Pwd}"
        }
        sh 'docker push uptimecareer/java-webapp-docker'
     }   
stage('Run Docker Image In Dev Server'){
        
        def dockerRun = ' docker run  -d -p 8080:8080 --name java-web-app uptimecareer/java-webapp-docker'
         
         sshagent(['docker_host']) {
          sh 'ssh -o StrictHostKeyChecking=no ec2-user@172.31.27.59 docker stop java-web-app || true'
          sh 'ssh  ec2-user@172.31.27.59 docker rm -f java-web-app || true'
          sh 'ssh  ec2-user@172.31.27.59 docker rmi -f  $(docker images -q) || true'
          sh "ssh  ec2-user@172.31.27.59 ${dockerRun}"
       }
}
}
