node{
    stage('Build')
    {
        git url:'https://github.com/PrasenjitM9/bwce6-cicd.git', credentialsId:'github-auth', branch:'main'
        withMaven(
            //mavenLocalRepo: "C:/Users/Simone Maletta/.m2/repository")
              mavenLocalRepo: ("C:/Users/Prasenjit Mahato/.m2/repository")
        {
            bat "mvn clean compile -f it.maletta.pipeline.tea.sample.application.parent/pom.xml"
        }
    }
    stage('Test')
    {
        withMaven(mavenLocalRepo: "C:/Users/Prasenjit Mahato/.m2/repository")
        {
            bat "mvn test -f it.maletta.pipeline.tea.sample.application.parent/pom.xml"
        }
    }
    stage('Deploy')
    {
        withMaven(mavenLocalRepo:"C:/Users/Prasenjit Mahato/.m2/repository")
        {
            bat "mvn install -f it.maletta.pipeline.tea.sample.application.parent/pom.xml -DearLocation=../it.maletta.pipeline.tea.sample.application/target"
        }
    }
}
