node{
    stage('Build')
    {
        git url:'https://github.com/SimoneMaletta83/tibcopipeline', credentialsId:'87887', branch:'main'
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
