pipeline {
    agent { label 'docker' }
        stages {
           stage('Pull Source code (BITBUCKET) ')
               {

                 steps
                  {
                    git url: 'https://github.com/sankojus/oreilly-docker-java-shopping.git',branch:'master', credentialsId: 'git-sankojus' ;
                  }
               }


           stage ('Build ( MAVEN ) ')
              {
                steps
                  {
                    sh 'mvn -v'

//sh 'export MAVEN_HOME=/var/lib/maven/apache-maven-3.5.3;export PATH=${PATH}:${MAVEN_HOME}/bin;mvn -X clean install -Dmaven.test.failure.ignore=true'
// def mavenVer = 'mvn -Dexec.executable=\'echo\' -Dexec.args=\'${project.version}\' --non-recursive exec:exec -q'             
              }
              }





          stage ('Upload to Artifactory')
              {

                steps
                   {
                    script {
                           //Artifactory server instance declaration

                           //Capturing the Build artifacts from Build server to upload
                              def uploadSpec = """{
                                  "files": [
                                             {
                                               "pattern": "${env.WORKSPACE}/shopfront/target/shopfront*.jar",
                                               "target": "getscicd-maven/AIM/${BUILD_LABEL}/",
                                               "recursive": "true"

                                             },

                                              {
                                                "pattern": "${env.WORKSPACE}/productcatalogue/target/productcatalogue*.jar",
                                                "target": "getscicd-maven/AIM/${BUILD_LABEL}/",
                                                "recursive": "true"
                                              },

                                              {
                                              "pattern": "${env.WORKSPACE}/stockmanager/target/stockmanager*.jar",
                                              "target": "getscicd-maven/AIM/${BUILD_LABEL}/",
                                               "recursive": "true"
                                              }

                                           ]
                              }"""

          server.upload(uploadSpec)

           }
          }
         }


            stage ('Docker Image build and Publish DTR')
             {
               steps
                 {
                   sh "./Publish_DockerRegistry.sh"
                 }
            }

            stage ('ICP login')
             {
               steps
                 {
                    withCredentials([usernamePassword(credentialsId: 'getsteamcicd', passwordVariable: 'password', usernameVariable: 'username')])
                                                {

                                                        sh "cloudctl login -a 'https://icp-cdl2.paascloud.oneadp.com:8443' -u \"${username}\" -p \"${password}\" -n k8-app"

                                                }
                 }
            }

          stage ( 'Run Kube commands')
                 {
                   steps
                   {
                     sh "./Deploy.sh"

                   }
                 }

}
}

