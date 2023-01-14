pipeline {
    agent any
    triggers { pollSCM('* * * * *') }
    stages {
        stage('vcs') {
            steps {
                git branch: 'dev', url: 'https://github.com/AnasAnsar1/StudentCoursesRestAPI.git'
            }
        }
        stage('Image_build_&_push') {
            agent { label 'Docker' }
            steps {
                sh "docker image build -t anasansarii/scra:$env.BRANCH_NAME-$env.BUILD_ID ."
                sh "docker image push anasansarii/scra:$env.BRANCH_NAME-$env.BUILD_ID"
            }
        }
        stage('deployment') {
            agent { label 'k8s' }
            steps {
                sh "cd deployments/overlays/$env.BRANCH_NAME/ && kustomize edit set image anasansarii/scra=anasansarii/scra:$env.BRANCH_NAME-$env.BUILD_ID"
                sh "kubectl apply -k deployments/overlays/$env.BRANCH_NAME/"
            }
        }
    }
}