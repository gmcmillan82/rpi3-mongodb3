#!groovy

/*
Author Graham McMillan
Email <graham.mcmillan@inflightdublin.com>
Date 31/01/2018
*/

pipeline {
  agent {
    node {
      label 'master'
    }
  }

  environment {
    TEST_IMAGE = 'reg.ifddev.com/arm/swift-mongonode:test'
    PROD_IMAGE = 'reg.ifddev.com/arm/swift-mongonode:latest'
	CONTAINER = 'mongo'
  }

  stages {
    stage('Cleanup') {
      steps {
        deleteDir()
      }
    }

    stage('Clone repository') {
      steps {
        checkout scm
      }
    }

    stage('Build ARM image') {
      steps {
        // Runs qemu container to enable building of arm images on x86_64
        sh 'docker run --rm --privileged multiarch/qemu-user-static:register --reset'
        sh 'docker build -t ${TEST_IMAGE} -f Dockerfile .'
      }
    }

    stage('Test ARM image') {
      steps {
        sh 'docker run --rm --name ${CONTAINER} ${TEST_IMAGE} mongo --version'
      }
    }

    //stage('Build x86 image') {
    //  steps {
    //    sh 'docker build -t ${X86_TEST_IMAGE} -f Dockerfile .'
    //  }
    //}

    //stage('Test x86 image') {
    //  steps {
    //    sh 'docker run --rm ${X86_TEST_IMAGE} node --version'
    //  }
    //}

    stage('Tag images') {
      steps {
        sh 'docker tag ${TEST_IMAGE} ${PROD_IMAGE}'
        //sh 'docker tag -f ${x86_TEST_IMAGE} ${env.x86_PROD_IMAGE}:${GIT_TAG_NAME}'
      }
    }

    stage('Push image') {
      steps {
        sh 'docker push ${PROD_IMAGE}'
      }
    }

    //stage('Clean up') {
    //  steps {
    //    sh 'docker rm dataimporter'
    //    sh 'docker rmi ${TEST_IMAGE}'
    //    sh 'docker rmi ${PROD_IMAGE}'
    //  }
    //}
  }
}
