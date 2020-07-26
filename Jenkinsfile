node('master') {
    def mvnhome
    stage('Poll') {
            checkout scm
            mvnhome=tool 'M3'
    }
    stage('Build & Unit test'){
            sh '/var/lib/jenkins/tools/hudson.tasks.Maven_MavenInstallation/M3/bin/mvn clean verify -DskipITs=true';
            junit '**/target/surefire-reports/TEST-*.xml'
            archive 'target/*.jar'
    }
    stage('Static Code Analysis'){
            sh '/var/lib/jenkins/tools/hudson.tasks.Maven_MavenInstallation/M3/bin/mvn clean verify sonar:sonar -Dsonar.projectName=example-project -Dsonar.projectKey=example-project -Dsonar.projectVersion=$BUILD_NUMBER';
    }
    stage ('Integration Test'){
            sh '/var/lib/jenkins/tools/hudson.tasks.Maven_MavenInstallation/M3/bin/mvn clean verify -Dsurefire.skip=true';
            junit '**/target/failsafe-reports/TEST-*.xml'
            archive 'target/*.jar'
    }
    stage ('Publish'){
            def server = Artifactory.server 'Default Artifactory Server'
            def uploadSpec = """{
"files": [
            {
            "pattern": "target/hello-0.0.1.war",
            "target": "example-project/${BUILD_NUMBER}/",
            "props": "Integration-Tested=Yes;Performance-Tested=No"
            }
        ]
    }"""
server.upload(uploadSpec)
    }
}
