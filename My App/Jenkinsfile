node {
  stage('SCM Checkout'){
    git 'https://github.com/Manash2712/my-app'
  }
  stage('Compile and Package'){
    sh 'mvn install'
  }
  stage('Email Notification'){
    mail bcc: '', body: '''Hi,
    This is test email from Jenkins Job.
    Thanks,
    Manash''', cc: '', from: '', replyTo: '', subject: 'Test Jenkins Job', to: 'manash@test.com'
  }
  stage('Slack Notification'){
    slackSend baseUrl: 'https://yourslackwebhookurl/', 
    channel: 'Test Channel', 
    message: 'Hi, testing slack send from Jenkins Pipeline', 
    tokenCredentialId: 'slack-demo'
  }
}
