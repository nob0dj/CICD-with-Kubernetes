node {
    APP_NAME = 'myapp'
    RELEASE = '1.0.0'
    DOCKER_USER = 'library'
    DOCKER_PASS = 'dockerlogin'
    IMAGE_NAME = "${DOCKER_USER}" + "/" + "${APP_NAME}"
    IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
    stage("Cleanup Workspace"){
        cleanWs()
    }
    stage("Checkout from SCM"){
        git branch: 'master', credentialsId: 'gitlab_idpwd', url: 'http://192.168.15.30/root/myapp.git'
    }
    stage("Build Application"){
        sh "mvn clean package"
    }
    stage("Test Application"){
        sh "mvn test"
    }
    stage("Build & Push Docker Image") {
        docker.withRegistry('http://harbor.ideacube.co.kr',DOCKER_PASS) {
            docker_image = docker.build "${IMAGE_NAME}"
        }
        docker.withRegistry('http://harbor.ideacube.co.kr',DOCKER_PASS) {
            docker_image.push("${IMAGE_TAG}")
            docker_image.push('latest')
        }
    }
    stage("SSH Into k8s Server") {
        def remote = [:]
        remote.name = 'k8s'
        remote.host = '192.168.15.101'
        remote.user = 'user1'
        remote.password = '1234'
        remote.allowAnyHosts = true
        
        stage('Put myapp-deployment.yml') {
            sshPut remote: remote, from: 'myapp-deployment.yml', into: '.'
        }
        stage('Deploy myapp') {
            sshCommand remote: remote, command: "kubectl apply -f myapp-deployment.yml"
        }
    }
}