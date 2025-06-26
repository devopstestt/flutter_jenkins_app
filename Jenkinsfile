pipeline {
  agent any

  environment {
    ANDROID_HOME = "/mnt/ebs/android-sdk"
    FLUTTER_HOME = "/mnt/ebs/flutter"
    PATH = "${FLUTTER_HOME}/bin:${ANDROID_HOME}/cmdline-tools/latest/bin:${ANDROID_HOME}/platform-tools:/usr/bin:/bin"
    JAVA_OPTS = "-Xmx2048m"
    GRADLE_OPTS = "-Dorg.gradle.daemon=false"
  }

  stages {
    stage('Checkout Code') {
      steps {
        git credentialsId: 'github-ssh', url: 'git@github.com:devopstestt/flutter_jenkins_app.git', branch: 'master'
      }
    }

    stage('Flutter Clean') {
      steps {
        sh 'flutter clean'
      }
    }

    stage('Flutter Pub Get') {
      steps {
        sh 'flutter pub get'
      }
    }

    stage('Build APK') {
      steps {
        sh '''
          flutter build apk --release --verbose | tee flutter_apk_build.log
        '''
      }
    }

    stage('Archive APK') {
      steps {
        script {
          def apkPath = 'build/app/outputs/flutter-apk/app-release.apk'
          if (fileExists(apkPath)) {
            archiveArtifacts artifacts: apkPath, fingerprint: true
          } else {
            echo "❌ APK not found at: ${apkPath}"
            currentBuild.result = 'FAILURE'
          }
        }
      }
    }

    stage('Build AAB') {
      steps {
        sh '''
          flutter build appbundle --release --verbose | tee flutter_aab_build.log
        '''
      }
    }

    stage('Archive AAB') {
      steps {
        script {
          def aabPath = 'build/app/outputs/bundle/release/app-release.aab'
          if (fileExists(aabPath)) {
            archiveArtifacts artifacts: aabPath, fingerprint: true
          } else {
            echo "❌ AAB not found at: ${aabPath}"
            currentBuild.result = 'FAILURE'
          }
        }
      }
    }

    stage('Deploy to Play Store') {
      steps {
        dir('android') {
          sh 'fastlane deploy'
        }
      }
    }
  }

  post {
    failure {
      echo '❌ Build failed. Check console output for details.'
    }
    success {
      echo '✅ APK and AAB built, archived, and deployed to Google Play Store successfully!'
    }
  }
}


