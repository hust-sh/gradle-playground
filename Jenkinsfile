pipeline {
  agent {
    node {
      label 'jenkins-slave'
    }
  }

  stages {
    stage('Checkout') {
      steps {
        deleteDir()
        checkout scm
      }
    }

    // stage('Test') {
    //   environment {
    //     SOURCE_PATH = "${env.WORKSPACE}/source/"
    //   }
    //   steps {
    //     script {
    //       sh '''
    //       echo $USER
    //       ls -lat
    //       cd $SOURCE_PATH
    //       ls -lat
    //       . ./build-test.sh
    //       . ./copy-coverage.sh
    //       '''
    //     }
    //   }
    //   post {
    //     always {
    //       step([$class: 'CoberturaPublisher',
    //             coberturaReportFile: 'source/coverage/cobertura-coverage.xml',
    //             failUnhealthy: true,
    //             failUnstable: true,
    //             lineCoverageTargets: '90.0, 80.1, 50'])
    //     }
    //   }
    // }
    stage("Build") {
      steps {
        script {
          sh '''
          ./gradlew build
          '''
        }
      }
    }

    stage('Test Coverage') {
      steps {
        echo 'Code Coverage'
        jacoco(
          minimumLineCoverage : '40',
          maximumLineCoverage: '100',
          changeBuildStatus: true
        )
      }
    }

    stage('OWASP Dependency Check') {
      steps {
        sh './gradlew dependencyCheckAnalyze --info'
        // dependencyCheckPublisher canComputeNew: false, defaultEncoding: '', healthy: '', pattern: '', unHealthy: ''
        // dependencyCheckPublisher pattern: 'build/reports/dependency-check-report.xml'
      }
    }

    // stage("Deploy") {
    //   environment {
    //     AWS_ID = credentials("aws-iam-user-for-pipeline")
    //     AWS_ACCESS_KEY_ID = "${env.AWS_ID_USR}"
    //     AWS_SECRET_ACCESS_KEY = "${env.AWS_ID_PSW}"
    //     AWS_DEFAULT_OUTPUT = 'json'
    //     S3_BUCKET_NAME = "s3://s3-static-site.yogiman.cn"
    //   }
    //   steps {
    //     script {
    //       sh '''
    //       pwd
    //       ls -lat
    //       aws s3 cp ./build/ $S3_BUCKET_NAME --recursive 
    //       '''
    //     }
    //   }
    // }
  }

  post {
    always {
      dependencyCheckPublisher pattern: 'build/reports/dependency-check-report.xml'
    }
  }
}
