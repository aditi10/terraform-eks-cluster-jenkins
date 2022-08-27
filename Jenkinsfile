pipeline {
    agent any
    stages {
        stage('clone repository and install') {
            steps {
                sh '''
                git clone --recurse-submodules https://github.com/mendix/docs
                npm install
curl -L https://github.com/gohugoio/hugo/releases/download/v0.91.0/hugo_extended_0.91.0_Linux-64bit.tar.gz | tar xz
chmod +x ./hugo


'''         }
        }

        stage('Run the server') {
            steps {
                sh '''hugo server --environment development'''

            }
        }

        stage('Deploy') {
            steps {
                sh '''Deploying the application'''
                sh '''echo "Starting sync to AWS"
                cd ./public
                aws s3 sync . s3://$TARGETAWSBUCKET --delete --only-show-errors --exclude "*.png" # sync all files except png files
                aws s3 sync . s3://$TARGETAWSBUCKET --delete --only-show-errors --size-only --exclude "*" --include "*.png" # sync all png files
                echo "Upload to AWS took $((SECONDS - start)) seconds" '''

            }
        }
    }
}