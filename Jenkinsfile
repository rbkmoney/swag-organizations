#!groovy
// -*- mode: groovy -*-

build('swag-organizations', 'docker-host') {
  checkoutRepo()
  loadBuildUtils('build-utils')

  def pipeDefault
  def withWsCache
  runStage('load pipeline') {
    env.JENKINS_LIB = "build-utils/jenkins_lib"
    pipeDefault = load("${env.JENKINS_LIB}/pipeDefault.groovy")
    withWsCache = load("${env.JENKINS_LIB}/withWsCache.groovy")
  }

  pipeDefault() {

    runStage('install-deps') {
      withWsCache("node_modules") {
        sh 'make wc_install'
      }
    }

    runStage('validate-spec') {
      sh 'make wc_validate'
    }

    runStage('build') {
      sh 'make wc_build'
    }

    // Java
    runStage('Build client & server') {
      withCredentials([[$class: 'FileBinding', credentialsId: 'java-maven-settings.xml', variable: 'SETTINGS_XML']]) {
        if (env.BRANCH_NAME == 'master' || env.BRANCH_NAME.startsWith('epic/')) {
          sh 'make SETTINGS_XML=${SETTINGS_XML} BRANCH_NAME=${BRANCH_NAME} java.swag.deploy_client'
          sh 'make SETTINGS_XML=${SETTINGS_XML} BRANCH_NAME=${BRANCH_NAME} java.swag.deploy_server'
        } else {
          sh 'make SETTINGS_XML=${SETTINGS_XML} BRANCH_NAME=${BRANCH_NAME} java.swag.compile_client'
          sh 'make SETTINGS_XML=${SETTINGS_XML} BRANCH_NAME=${BRANCH_NAME} java.swag.compile_server'
        }
      }
    }

  }
}
