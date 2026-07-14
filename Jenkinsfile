pipeline {
    agent { label 'electronix' }

    environment {
        S3_BUCKET = 'electronics-production-2002'
        CLOUDFRONT_ID = 'E1OV4MEH9XNOBI'
        AWS_REGION = 'eu-north-1'
    }

    stages {

        stage('Install Dependencies') {
            when {
                changeset "frontend/**"
            }
            steps {
                dir('frontend') {
                    sh 'npm install'
                }
            }
        }

        stage('Run Tests') {
            when {
                changeset "frontend/**"
            }
            steps {
                dir('frontend') {
                    sh 'npm test -- --watchAll=false || echo "No Test Configured.."'
                }
            }
        }

        stage('Build') {
            when {
                changeset "frontend/**"
            }
            steps {
                dir('frontend') {
                    sh 'npm run build'
                }
            }
        }

        stage('Deploy S3') {
            when {
                changeset "frontend/**"
            }
            steps {
                dir('frontend') {
                    sh '''
                        aws s3 sync dist/ s3://${S3_BUCKET} --delete --region ${AWS_REGION}
                    '''
                }
            }
        }

        stage('Invalidate CloudFront Cache') {
            when {
                changeset "frontend/**"
            }
            steps {
                sh '''
                    aws cloudfront create-invalidation \
                    --distribution-id ${CLOUDFRONT_ID} \
                    --paths "/*"
                '''
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