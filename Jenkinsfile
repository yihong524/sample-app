pipeline {
  agent {
    kubernetes {
      label 'mypod'
      defaultContainer 'jnlp'
      yaml """
apiVersion: v1
kind: Pod
metadata:
  labels:
    some-label: some-label-value
spec:
  containers:
  - name: maven
    image: 192.168.200.21:30797/maven:3.3.9-jdk-8-alpine
    imagePullPolicy: Always
    command:
    - cat
    tty: true
  - name: docker
    image: docker:17.03
    imagePullPolicy: Always
    command:
    - cat
    tty: true
    volumeMounts:
    - name: docker
      mountPath: /var/run/docker.sock
  volumes:
  - name: docker
    hostPath:
      path: /var/run/docker.sock
"""
    }
  }
  stages {
    stage('Run maven') {
      steps {
        git 'git@git.of.com:payment/digitalPay.git', credentialsId: '3f487004-23c2-4c77-a47d-098e5c10b95e', branch: 'master'
        container('maven') {
          sh 'mvn clean package -P test -DskipTests -U'
          sh 'ls -l target'
        }
        container('docker') {
          sh 'pwd'
          sh 'ls'
        }
      }
    }
  }
}

/////////////////
// {
//   node(label) {
//     def myRepo = checkout scm
//     def gitCommit = myRepo.GIT_COMMIT
//     def gitBranch = myRepo.GIT_BRANCH
//     def shortGitCommit = "${gitCommit[0..10]}"
//     def previousGitCommit = sh(script: "git rev-parse ${gitCommit}~", returnStdout: true)
 
//     stage('Test') {
//       try {
//         container('gradle') {
//           sh """
//             pwd
//             echo "GIT_BRANCH=${gitBranch}" >> /etc/environment
//             echo "GIT_COMMIT=${gitCommit}" >> /etc/environment
//             gradle test
//             """
//         }
//       }
//       catch (exc) {
//         println "Failed to test - ${currentBuild.fullDisplayName}"
//         throw(exc)
//       }
//     }
//     stage('Build') {
//       container('gradle') {
//         sh "gradle build"
//       }
//     }
//     stage('Create Docker images') {
//       container('docker') {
//         withCredentials([[$class: 'UsernamePasswordMultiBinding',
//           credentialsId: 'dockerhub',
//           usernameVariable: 'DOCKER_HUB_USER',
//           passwordVariable: 'DOCKER_HUB_PASSWORD']]) {
//           sh """
//             docker login -u ${DOCKER_HUB_USER} -p ${DOCKER_HUB_PASSWORD}
//             docker build -t namespace/my-image:${gitCommit} .
//             docker push namespace/my-image:${gitCommit}
//             """
//         }
//       }
//     }
//     stage('Run kubectl') {
//       container('kubectl') {
//         sh "kubectl get pods"
//       }
//     }
//     stage('Run helm') {
//       container('helm') {
//         sh "helm list"
//       }
//     }
//   }
// }



// node {
//   def project = 'REPLACE_WITH_YOUR_PROJECT_ID'
//   def appName = 'gceme'
//   def feSvcName = "${appName}-frontend"
//   def imageTag = "gcr.io/${project}/${appName}:${env.BRANCH_NAME}.${env.BUILD_NUMBER}"

//   checkout scm

//   stage 'Build image'
//   sh("docker build -t ${imageTag} .")

//   stage 'Run Go tests'
//   sh("docker run ${imageTag} go test")

//   stage 'Push image to registry'
//   sh("gcloud docker -- push ${imageTag}")

//   stage "Deploy Application"
//   switch (env.BRANCH_NAME) {
//     // Roll out to canary environment
//     case "canary":
//         // Change deployed image in canary to the one we just built
//         sh("sed -i.bak 's#gcr.io/cloud-solutions-images/gceme:1.0.0#${imageTag}#' ./k8s/canary/*.yaml")
//         sh("kubectl --namespace=production apply -f k8s/services/")
//         sh("kubectl --namespace=production apply -f k8s/canary/")
//         sh("echo http://`kubectl --namespace=production get service/${feSvcName} --output=json | jq -r '.status.loadBalancer.ingress[0].ip'` > ${feSvcName}")
//         break

//     // Roll out to production
//     case "master":
//         // Change deployed image in canary to the one we just built
//         sh("sed -i.bak 's#gcr.io/cloud-solutions-images/gceme:1.0.0#${imageTag}#' ./k8s/production/*.yaml")
//         sh("kubectl --namespace=production apply -f k8s/services/")
//         sh("kubectl --namespace=production apply -f k8s/production/")
//         sh("echo http://`kubectl --namespace=production get service/${feSvcName} --output=json | jq -r '.status.loadBalancer.ingress[0].ip'` > ${feSvcName}")
//         break

//     // Roll out a dev environment
//     default:
//         // Create namespace if it doesn't exist
//         sh("kubectl get ns ${env.BRANCH_NAME} || kubectl create ns ${env.BRANCH_NAME}")
//         // Don't use public load balancing for development branches
//         sh("sed -i.bak 's#LoadBalancer#ClusterIP#' ./k8s/services/frontend.yaml")
//         sh("sed -i.bak 's#gcr.io/cloud-solutions-images/gceme:1.0.0#${imageTag}#' ./k8s/dev/*.yaml")
//         sh("kubectl --namespace=${env.BRANCH_NAME} apply -f k8s/services/")
//         sh("kubectl --namespace=${env.BRANCH_NAME} apply -f k8s/dev/")
//         echo 'To access your environment run `kubectl proxy`'
//         echo "Then access your service via http://localhost:8001/api/v1/proxy/namespaces/${env.BRANCH_NAME}/services/${feSvcName}:80/"
//   }
// }


// another example
// def label = "worker-${UUID.randomUUID().toString()}"

// podTemplate(label: label, containers: [
//   containerTemplate(name: 'gradle', image: 'gradle:4.5.1-jdk9', command: 'cat', ttyEnabled: true),
//   containerTemplate(name: 'docker', image: 'docker', command: 'cat', ttyEnabled: true),
//   containerTemplate(name: 'kubectl', image: 'lachlanevenson/k8s-kubectl:v1.8.8', command: 'cat', ttyEnabled: true),
//   containerTemplate(name: 'helm', image: 'lachlanevenson/k8s-helm:latest', command: 'cat', ttyEnabled: true)
// ],
// volumes: [
//   hostPathVolume(mountPath: '/home/gradle/.gradle', hostPath: '/tmp/jenkins/.gradle'),
//   hostPathVolume(mountPath: '/var/run/docker.sock', hostPath: '/var/run/docker.sock')
// ]) {
//   node(label) {
//     def myRepo = checkout scm
//     def gitCommit = myRepo.GIT_COMMIT
//     def gitBranch = myRepo.GIT_BRANCH
//     def shortGitCommit = "${gitCommit[0..10]}"
//     def previousGitCommit = sh(script: "git rev-parse ${gitCommit}~", returnStdout: true)
 
//     stage('Test') {
//       try {
//         container('gradle') {
//           sh """
//             pwd
//             echo "GIT_BRANCH=${gitBranch}" >> /etc/environment
//             echo "GIT_COMMIT=${gitCommit}" >> /etc/environment
//             gradle test
//             """
//         }
//       }
//       catch (exc) {
//         println "Failed to test - ${currentBuild.fullDisplayName}"
//         throw(exc)
//       }
//     }
//     stage('Build') {
//       container('gradle') {
//         sh "gradle build"
//       }
//     }
//     stage('Create Docker images') {
//       container('docker') {
//         withCredentials([[$class: 'UsernamePasswordMultiBinding',
//           credentialsId: 'dockerhub',
//           usernameVariable: 'DOCKER_HUB_USER',
//           passwordVariable: 'DOCKER_HUB_PASSWORD']]) {
//           sh """
//             docker login -u ${DOCKER_HUB_USER} -p ${DOCKER_HUB_PASSWORD}
//             docker build -t namespace/my-image:${gitCommit} .
//             docker push namespace/my-image:${gitCommit}
//             """
//         }
//       }
//     }
//     stage('Run kubectl') {
//       container('kubectl') {
//         sh "kubectl get pods"
//       }
//     }
//     stage('Run helm') {
//       container('helm') {
//         sh "helm list"
//       }
//     }
//   }
// }