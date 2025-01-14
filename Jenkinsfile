pipeline {
    agent any
    environment{   
        AWS_S3_BUCKET = "lamaalawwad-s3-bilt3"
        ARTIFACT_NAME = "hello-world.war"
        AWS_ACCESS_KEY_ID     = credentials('LamaAlawwad-aws-secret-key-id')
        AWS_SECRET_ACCESS_KEY = credentials('LamaAlawwad-aws-secret-access-key')
        AWS_EB_APP_NAME = "Debbugers"
        AWS_EB_APP_VERSION = "${BUILD_ID}"
        AWS_EB_ENVIRONMENT = "Debbugers-env"
    
    }

    stages {
        

   

        stage('Validate') {
            steps {
                sh "mvn validate"
                sh "mvn clean"
            }
        }

        stage('Build') {
            steps {
                sh "mvn compile"
            }
        }

        stage('Test') {
            steps {
                sh "mvn test"
                
            }
      
        }


        stage('Package') {
            steps {
                sh "mvn package"
                
            }
            post{
                success{
                    archiveArtifacts artifacts: '**/target/**.war', followSymlinks: false
                }
            
        }
        }
    
        stage('publish artfacts to s3') {
            steps {
                sh "aws configure set region us-east-1" 
                sh "aws s3 cp ./target/**.war s3://$AWS_S3_BUCKET/$ARTIFACT_NAME"
            }
        }
        
            stage('db') {
            steps {
                sh "mvn spring-boot:run"
                
            }
       
        }

        stage('Deploy') {
            steps {

                sh 'aws elasticbeanstalk create-application-version --application-name $AWS_EB_APP_NAME --version-label $AWS_EB_APP_VERSION --source-bundle S3Bucket=$AWS_S3_BUCKET,S3Key=$ARTIFACT_NAME'

                sh 'aws elasticbeanstalk update-environment --application-name $AWS_EB_APP_NAME --environment-name $AWS_EB_ENVIRONMENT --version-label $AWS_EB_APP_VERSION'
            }
        }

    
}
    }
