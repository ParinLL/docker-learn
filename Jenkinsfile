podTemplate(yaml: """
kind: Pod
spec:
  containers:
  - name: kaniko
    #image: lmakarov/kaniko
    image: gcr.io/kaniko-project/executor:debug
    env:
    - name: imageTag
      value: ${env.imageTag}
    imagePullPolicy: Always
    command:
    - /busybox/cat
    tty: true
    volumeMounts:
      - name: jenkins-docker-cfg
        mountPath: /kaniko/.docker
  volumes:
  - name: jenkins-docker-cfg
    projected:
      sources:
      - secret:
          name: docker-credentials
          items:
            - key: .dockerconfigjson
              path: config.json
"""
  ) {

  node(POD_LABEL) {
    stage('Build with Kaniko') {
      git 'https://github.com/harryliu123/docker-learn'
	  env.imageTag = sh (script: 'git rev-parse --short HEAD ${GIT_COMMIT}', returnStdout: true).trim()
      sh 'echo ${imageTag}'
      container('kaniko') {
        sh 'ls && /kaniko/executor -f `pwd`/Dockerfile -c `pwd` --insecure --skip-tls-verify --cache=true --destination=harryliu123/java-test:${imageTag}'
      }
    }
  }
}