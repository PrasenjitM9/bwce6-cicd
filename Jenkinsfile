node{
    stage('Build')
    {
        git url:'https://github.com/PrasenjitM9/bwce6-cicd.git', credentialsId:'github-auth', branch:'main'
        withMaven(mavenLocalRepo: "C:/Users/Prasenjit Mahato/.m2/repository")
        {
            dir('cicd-demo.module.application.parent') {
            bat "mvn clean compile"
            }
        }
    }
    stage('Test')
    {
        withMaven(mavenLocalRepo: "C:/Users/Prasenjit Mahato/.m2/repository")
        {
            dir('cicd-demo.module.application.parent') {
                
            bat "mvn test"
            }
        }
    }
    stage('Deploy')
    {
        withMaven(mavenLocalRepo:"C:/Users/Prasenjit Mahato/.m2/repository")
        {
            dir('cicd-demo.module.application.parent') {
            bat "mvn install"
            }
        }
    }
}
