pipeline{

agent any

stages{

 stage("Clone code") {
            steps {
                script {
                    // Let's clone the source
                    git 'https://github.com/sriharsha219/complete-pipeline.git';
                }
            }
        }

stage('Compile'){

steps{

echo "Compiling sample maven-webapp"
sh "mvn clean compile"
echo "Project compiled"
    }
}

stage('SonarQube Analysis'){

steps{

echo "Sending code for SonarQube analysis"
sh "mvn sonar:sonar -Dsonar.host.url=http://3.130.69.246 -Dsonar.login=73fb4a283811a21ea3cfe95b9930ba698b5072ab"
echo "Project uploaded to SonarQube"
echo "Check results in SonarQube"
input("Do you want to proceed ?")
    }
}

stage('Test'){

steps{

echo "Testing sample maven-webapp"
sh "mvn clean test"
echo "Project tested"
    }
}
stage('Package'){

steps{

echo "Packing sample maven-webapp"
sh "mvn package"
echo "Project packed"
    }
}
stage('Install'){

steps{

echo "Installing sample maven-webapp"
sh "mvn install"
echo "Project installed"
    }
}

 stage("Publish to nexus") {
            steps {
                script {
                    // Read POM xml file using 'readMavenPom' step , this step 'readMavenPom' is included in: https://plugins.jenkins.io/pipeline-utility-steps
                    pom = readMavenPom file: "pom.xml";
                        nexusArtifactUploader(
                        nexusVersion: 'nexus3',
    protocol: 'http',
    nexusUrl: '3.130.67.158:8081/nexus/content/repositories/my-nexus-snapshots',
    groupId: pom.groupId,
    version: pom.version,
    repository: 'my-nexus-snapshots',
    credentialsId: 'Nexus',
    artifacts: [
        [artifactId: 'sample-maven-webapp',
         classifier: '',
         file: '/home/ec2-user/.m2/repository/org/sample-maven-webapp/1.0-SNAPSHOT/sample-maven-webapp-1.0-SNAPSHOT.war',
         type: 'war']

    ]
             )
           }
        }

stage('Deploy-Staging'){

steps{
echo "Deploying war file from nexus to staging machine"
sh "cd /home/ec2-user/"
sh "wget http://3.130.67.158:8081/nexus/service/local/repositories/my-nexus-snapshots/content/repository/my-nexus-snapshots/org/sample-maven-webapp/1.0-SNAPSHOT/sample-maven-webapp-1.0-20190603.035843-1.war"
sh "scp -i EC2-Keypair.pem sample-maven-webapp-1.0-20190603.035843-1.war ec2-user@3.130.67.158:8080/"
echo "deployment to staging completed"
      }
   }
}
post {
        always {
            echo 'One way or another, I have finished'
            deleteDir() /* clean up our workspace */
        }
        success {
            echo 'I succeeeded!'
        }
        unstable {
            echo 'I am unstable :/'
        }
        failure {
            echo 'I failed :('
        }
        changed {
            echo 'Things were different before...'
        }
    }
}
