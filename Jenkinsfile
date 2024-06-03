def registry = 'https://devopspra.jfrog.io'

pipeline {
    agent {label 'maven'}


environment {
    PATH = "/opt/apache-maven-3.9.4/bin:$PATH"
    }
    stages{
       stage("build"){
        steps {
            echo "------- build started --------"
            sh 'mvn clean deploy '
            echo "---------build completed ----------"
        } 
    }
    

stage("test"){
        steps{
            echo "--------unit test started ---------"
            sh 'mvn surefire-report:report'
            echo "---------unit test completed -------"
        }
    }

    
}

stage("Jar Publish") {
        steps {
            script {
                    echo '<--------------- Jar Publish Started --------------->'
                     def server = Artifactory.newServer url:registry+"/artifactory" ,  credentialsId:"jfrogartifact-credentials"
                     def properties = "buildid=${env.BUILD_ID},commitid=${GIT_COMMIT}";
                     def uploadSpec = """{
                          "files": [
                            {
                              "pattern": "jarstaging/(*)",
                              "target": "devopspra-libs-release-local/{1}",
                              "flat": "false",
                              "props" : "${properties}",
                              "exclusions": [ "*.sha1", "*.md5"]
                            }
                         ]
                     }"""
                     def buildInfo = server.upload(uploadSpec)
                     buildInfo.env.collect()
                     server.publishBuildInfo(buildInfo)
                     echo '<--------------- Jar Publish Ended --------------->'  
            
            }
        }   
    } 
}
