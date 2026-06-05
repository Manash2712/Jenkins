currentBuild.displayName = "online-shopping-#"+currentBuild.number
pipeline{
  agent any
  tools {
    maven 'maven3'
  }
  stages{
    stage('Git Checkout'){
      steps{
        git 'https://github.com/Manash2712/myweb'
      }
    }
    stage('Maven build'){
      steps{
        sh 'mvn clean package'
        sh 'mv target/*war target/myweb.war'
      }
    }
    stage('Deploy-Dev'){
      steps{
        sshagent(['dev-server']) {
            sh """
              scp -o StrictHostKeyChecking=no target/myweb.war ec2-user@172.31.12.214:/var/lib/tomcat9/webapps/ 
              ssh ec2-user@172.31.12.214 sudo systemctl restart tomcat9
            """
        }
      }
    }
  }
}
