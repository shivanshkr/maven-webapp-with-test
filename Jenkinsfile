pipeline{
    agent any
    stages{
        stage('Setup'){
            steps{
                echo 'Setup'
                bat 'mvn clean'
            }
        }
        stage('Compile'){
            steps{
                echo 'Compile'
                bat 'mvn compile'
            }
        }

        stage('Static Analysis'){
            steps{
                echo 'Static Analysis'
                bat 'mvn sonar:sonar'
            }
        }
        stage('Unit Testing'){
            steps{
                echo 'Unit Testing'
                bat 'mvn test'
            }
        }
        stage('Code Coverage'){
            steps{
                echo 'Code Coverage'
                bat 'mvn cobertura:cobertura'
            }
        }
        stage('War'){
            steps{
                echo 'generating War'
                bat 'mvn war:war'
            }
        }
        stage('To artifactory'){
            steps{
                echo 'To JFrog artifactory'
                bat 'mvn deploy'

            }
        }
        // stage('SmokeTest'){
        //     steps{
        //         echo 'Publishing to tomcat'
        //         deploy adapters: [tomcat8(credentialsId: 'e567d2b6-7b4a-4c73-ade9-e34b7f92f001', path: '', url: 'http://localhost:9090/')], contextPath: '/myapp2', war: 'target/*.war'
        //     }
        // }
        stage('Deploy') {
            steps {
                step([$class: 'UCDeployPublisher',
                    siteName: 'udeploy-server',
                    component: [
                        $class: 'com.urbancode.jenkins.plugins.ucdeploy.VersionHelper$VersionBlock',
                        componentName: 'DemoApp-APP',
                        delivery: [
                            $class: 'com.urbancode.jenkins.plugins.ucdeploy.DeliveryHelper$Push',
                            pushVersion: '${BUILD_NUMBER}',
                            baseDir: 'workspace\\deployWithUCDpipeline',
                            fileIncludePatterns: '**/*',
                            fileExcludePatterns: '',
                            pushDescription: 'Pushed from Jenkins'
                        ]
                    ]
                ])
                step([$class: 'UCDeployPublisher',
                    siteName: 'udeploy-server',
                    deploy: [
                        $class: 'com.urbancode.jenkins.plugins.ucdeploy.DeployHelper$DeployBlock',
                        deployApp: 'DemoApp',
                        deployEnv: 'Test 1',
                        deployProc: 'DemoApp Process',
                        deployVersions: 'DemoApp-APP:${BUILD_NUMBER}',
                        deployOnlyChanged: false
                    ]
                ])
            }
        }
      }
    post {
        always {
            archiveArtifacts '**/*'
            junit 'target/surefire-reports/*.xml'

        }
    }
}
