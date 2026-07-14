pipeline{
    agent { label 'electronix'}

    environment{
        S3_BUCKET='electronix-production-bucket'
        CLOUDFRONT_ID='E1BVAN0AQNRTVB'
        AWS_REGION='us-east-1'
    }

    stages {

        stage('Frontend Deployment') {

            // when {
            //     changeset "frontend/**"
            // }

            stages {

                stage('Install Dependencies') {
                    steps {
                        dir('frontend') {
                            sh '''
                                npm install
                            '''
                        }
                    }
                }

                stage('Run Tests') {
                    steps {
                        dir('frontend') {
                            sh '''
                                npm test -- --watchAll=false || echo "No Test Configured.."
                            '''
                        }
                    }
                }

                stage('Build') {
                    steps {
                        dir('frontend') {
                            sh '''
                                npm run build
                            '''
                        }
                    }
                }

                stage('Deploy to S3') {
                    steps {
                        dir('frontend') {
                            sh '''
                                aws s3 sync dist/ s3://${S3_BUCKET} --delete --region ${AWS_REGION}
                            '''
                        }
                    }
                }

                stage('Invalidate CloudFront Cache') {
                    steps {
                        sh '''
                            aws cloudfront create-invalidation \
                              --distribution-id ${CLOUDFRONT_ID} \
                              --paths "/*"
                        '''
                    }
                }

            }
        }
    }

    post {
        success {
            echo 'Frontend Deployment Successful ✅'
        }

        failure {
            echo 'Frontend Deployment Failed ❌'
        }
    }
}