node('docker') {
    stage 'checkout source' {
        git url: 'https://github.com/marhan/rpi-nexus3.git', branch: 'master'
    }

    stage 'build image' {
        sh 'make -B private-build'
    }

    stage 'push image' {
        sh 'make private-push'
    }
}    