pipeline {
    agent any

    environment {
        ANDROID_HOME = "/mnt/ebs/android-sdk"
        PATH = "/mnt/ebs/flutter/bin:$ANDROID_HOME/cmdline-tools/latest/bin:$ANDROID_HOME/platform-tools:$PATH"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git credentialsId: 'github-ssh', url: 'git@github.com:devopstestt/flutter_jenkins_app.git', branch: 'master'
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
            export JAVA_OPTS="-Xmx2048m"
            export GRADLE_OPTS="-Dorg.gradle.daemon=false"
            flutter build apk --verbose
        '''
    }
}

        stage('Archive APK') {
            steps {
                archiveArtifacts artifacts: 'build/app/outputs/flutter-apk/app-release.apk', fingerprint: true
            }
        }
    }
}
