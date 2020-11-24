def userInput
try {
    userInput = input(
        id: 'Proceed1', message: 'Was this successful?', parameters: [
        [$class: 'BooleanParameterDefinition', defaultValue: true, description: '', name: '請確認是否完成此工作: Proceed繼續, Abort取消']
        ])
} catch(err) { // input false
    def user = err.getCauses()[0].getUser()
    userInput = false
    echo "Aborted by: [${user}]"
}



podTemplate(yaml: """
kind: Pod
spec:
  containers:
  - name: kaniko
    #image: lmakarov/kaniko
    image: gcr.io/kaniko-project/executor:debug
    env:
    - name: COMMITTag
      value: ${env.COMMITTag}
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
  if (userInput == true) {
    withEnv(['gitci=github.com/harryliu123/docker-learn.git',
            'gitcd=github.com/harryliu123/argocd-example-apps.git',
			'gitcdproject=argocd-example-apps',
            'branch=master',
            'gitcdaddfile=guestbook/guestbook-ui-deployment.yaml',
            'imagename=harryliu123/java-test']) {
      stage('Build with Kaniko') {
      //  sh 'printenv'
        git url: "https://${gitci}", branch: "${branch}"
	    env.COMMITTag = sh (script: 'git rev-parse --short HEAD ${GIT_COMMIT}', returnStdout: true).trim()
        sh 'echo ${COMMITTag}'
        container('kaniko') {
          sh 'ls && /kaniko/executor -f `pwd`/Dockerfile -c `pwd` --insecure --skip-tls-verify --cache=true --destination=${imagename}:${COMMITTag}-`cat imagesversion`'
        }
      }
  
      stage('push to CD git') {
  
        git url: "https://${gitci}", branch: "${branch}"
        env.COMMITTag = sh (script: 'git rev-parse --short HEAD ${GIT_COMMIT}', returnStdout: true).trim()
	    sh 'echo ${COMMITTag}'
        env.imageversion = sh (script: 'cat imagesversion', returnStdout: true).trim()
        sh 'echo ${imageversion}'
        withCredentials([usernamePassword(credentialsId: 'cd-github', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
            sh('''
            git config --global user.email "harry.liu@tradevan.com.tw"
            git config --global user.name "jenkins"
            git config --local credential.helper "!f() { echo username=\\$GIT_USERNAME; echo password=\\$GIT_PASSWORD; }; f"
            git clone https://$gitcd
            cd $gitcdproject
            git checkout $branch
            sed -i "s@image: .*@image: ${imagename}:${COMMITTag}-${imageversion}@g" ${gitcdaddfile}
            cat ${gitcdaddfile}
            git add .
            git commit -m ${COMMITTag}
            git push -b $branch https://${GIT_USERNAME}:${GIT_PASSWORD}@$gitcd 
            ''')
        }
      }
	}
  }
}
}