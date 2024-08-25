pipeline {
    agent any       
    environment { 
            registry = "https://hub.docker.com/jeetdeveloper/bwce-cicd"
            IMAGE = "bwce_cicd.jenkins-build"
            VERSION = "${BUILD_NUMBER}"
            DOCKERHUB_CREDENTIALS=credentials('docker-hub-personal')
            PROJECT_NAME = ""
        }

    stages {
        stage(' Git ') 
        {
            parallel {
                stage('Git clone') {
                    steps {
                            echo '########################### Cloning code from  GitHub... ###########################'
                            // Get some code from a GitHub repository
                            git branch: 'main', url: 'https://github.com/PrasenjitM9/bwce6-cicd.git'
                    }
                }
            }
        }
        stage("sonar quality check"){
            agent {
                docker {
                    image 'openjdk:11'
                }
            }
            steps{
                script{
                    withSonarQubeEnv(credentialsId: 'sonar-token') {
                            sh 'chmod +x gradlew'
                            sh './gradlew sonarqube'
                    }

                    timeout(time: 1, unit: 'HOURS') {
                      def qg = waitForQualityGate()
                      if (qg.status != 'OK') {
                           error "Pipeline aborted due to quality gate failure: ${qg.status}"
                      }
                    }

                }  
            }
        }
             
        stage('Unit Test ')
        {
        //withMaven(mavenLocalRepo: "/home/ec2-user/.m2/repository")
           steps
           {
           // Run Maven on a Unix agent.
            echo '########################### Perform Unit Tests... ###########################'
    
            dir('cicd-demo.module.application.parent') 
            {
               // sh "ls -lrt"
                // some block
                //sh "mvn clean"
                bat "mvn clean"
            }
            }
        }

    stage(' App Build ')
        {
           steps
           {
           // Run Maven on a Unix agent.
            echo '###########################Building Application EAR.. ###########################'
            bat "mvn -f cicd-demo.module.application.parent package"
            }
        }
               
        stage('Artifactory')
        {
            steps
            {
                echo '########################### Move deployables to Artifacotory Server.. ###########################'  
                dir('cicd-demo.module.application.parent') 
                {
                   // sh "mvn install"
                }
                echo '########################### Artifacts are published to artifactory server... ###########################' 
            }
        }
               
        stage('App Image Processing')
        {
            steps
            {
             dir('src/') 
            {
                //sh "pwd"
                //sh "ls -lrt"
    
                echo '########################### Start Creating Application Image -- ' +"${IMAGE}:${VERSION}"+' -- ###############################'
                
               // bat 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'    //docker hub login       
                
                //bat "docker build -t ${IMAGE}:${VERSION} ."
                
                //multi architecture image build support through buildx
                
                bat 'docker buildx build --platform linux/arm64,linux/amd64 -t jeetdeveloper/${IMAGE}:${VERSION} --push .'
                
                echo '########################### App Image --  ' +"${IMAGE}:${VERSION}"+'  -- build Successfully ###############################'

                echo '########################### Push an App Image --  ' +"${IMAGE}:${VERSION}"+'  -- to Registry (DockerHub)... #######################'
                            
                bat 'docker tag ${NAME}:${IMAGE} jeetdeveloper/${IMAGE}:${VERSION}'  
                            
                // sh 'docker tag${IMAGE}:${VERSION} mpandav/${IMAGE}:${VERSION}'
                // sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'    //push to docker hub registry       
                bat 'docker push jeetdeveloper/${IMAGE}:${VERSION}'
                              
                // sh 'docker image rm -f ${IMAGE}:${VERSION}'   //clean up of dangling images
                              
                //sh "docker push 10.211.55.4:5000/$${IMAGE}:${VERSION}". Local Docker Registry
                
                echo '########################### App Image --  ' +"${IMAGE}:${VERSION}"+'  -- published to Registry successfully... #######################'
            }

            }
            post {
                success {
                //junit '**/target/surefire-reports/TEST-*.xml'#
                dir('src/cicd-demo.module.application'){
                     // some block
                     archiveArtifacts 'target/*.*'
                }
                echo '######################### Archieve all artifacts... ##########################'   
                }
            }
        }
                
                
        stage('K8s Deplyoment')
        {
            steps
            {
                echo '####################### Create Deployment for App -- ' +"${IMAGE}:${VERSION}"+'  -- in K8S Cluster... #########################'  

                //sh 'kubectl version'
                // exportign variables image and version so it can dynamically updated to manifest for each run
                bat 'export IMAGE="${IMAGE}"'
                bat 'export VERSION="${VERSION}"'
        
                bat 'envsubst < src/manifest.yaml | kubectl apply -f -'
               
                //create k8s deployment for app
                //sh 'kubectl apply -f src/manifest.yaml'
             }
        }

    }
}
