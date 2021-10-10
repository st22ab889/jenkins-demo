node("hello-ops"){
    stage('Prepare') {
        echo "1.Prepare Stage"
        //
        checkout scm
        script {
            build_tag = sh(returnStdout: true, script: 'git rev-parse --short HEAD').trim()
            if (env.BRANCH_NAME != 'master') {
                build_tag = "${env.BRANCH_NAME}-${build_tag}"
            }
        }
    }
    stage('Test') {
      echo "2.Test Stage"
    }
    stage('Build') {
        echo "3.Build Docker Image Stage"
        sh "docker build -t st22ab889/jenkins-demo:${build_tag} ."
    }
    stage('Push') {
        echo "4.Push Docker Image Stage"
        // push 到 docker hub 这一步非常慢
        //withCredentials([usernamePassword(credentialsId: 'docekrAuth', passwordVariable: 'dockerPassword', usernameVariable: 'dockerUser')]) {
        //    sh "docker login -u ${dockerUser} -p ${dockerPassword}"
        //    sh "docker push st22ab889/jenkins-demo:${build_tag}"
        //}
    }
    stage('Deploy') {
        echo "5. Deploy Stage"
        if (env.BRANCH_NAME == 'master') {
            input "please confirm deploy to Prod env ?"
        }
		
        sh "sed -i 's/<BUILD_TAG>/${build_tag}/' k8s-jenkins-go-demo.yaml"
        sh "sed -i 's/<BRANCH_NAME>/${env.BRANCH_NAME}/' k8s-jenkins-go-demo.yaml"

        sh "cat k8s-jenkins-go-demo.yaml"
        sh "kubectl apply -f k8s-jenkins-go-demo.yaml --record"
    }
}
