# Jenkins Ansible Role

Ansible Role para el despliegue de _Jenkins_ usando el Chart de Helm

# Ejemplo de ejecución individual del Rol

````bash
---
- hosts: localhost
  roles:
    - role: jenkins
      vars:
        namespace: devops
        output_path: /tmp/ansible_tasks
        desired_deployment_name: jenkins
        helmchart_path: /tmp/helm_charts/sunat-cuc-jenkins-helmchart/

        ingress_certificate_secret: sunat-wildcard-secret

        admin_user_credentials_secret: jenkins-admin-credentials-secret
        admin_user_credentials_secret_userKey: jenkins-admin-user
        admin_user_credentials_secret_passwordKey: jenkins-admin-password

        jenkins_image: jenkins/jenkins
        jenkins_imageTag: 2.253-jdk11
        jenkins_imagePullPolicy: IfNotPresent

        jenkins_agentsCap: 15
        jenkins_persistence: |
          storageClass: gp2
          accessMode: "ReadWriteOnce"
          size: "10Gi"

        jenkins_installPlugins: |
          - jdk-tool:1.4
          - workflow-support:3.5
          - matrix-project:1.17
          - jaxb:2.3.0.1
          - lockable-resources:2.8
          - credentials-binding:1.23
          - metrics:4.0.2.6
          - workflow-durable-task-step:2.36
          - cloudbees-folder:6.14
          - ace-editor:1.1
          - command-launcher:1.4
          - workflow-cps-global-lib:2.17
          - workflow-step-api:2.22
          - pipeline-model-extensions:1.7.1
          - authentication-tokens:1.4
          - git-server:1.9
          - workflow-aggregator:2.6
          - pipeline-stage-step:2.5
          - plain-credentials:1.7
          - echarts-api:4.8.0-2
          - ssh-credentials:1.18.1
          - pipeline-model-api:1.7.1
          - workflow-scm-step:2.11
          - prometheus:2.0.7
          - workflow-job:2.39
          - configuration-as-code:1.43
          - external-monitor-job:1.7
          - jsch:0.1.55.2
          - greenballs:1.15
          - display-url-api:2.3.3
          - branch-api:2.5.9
          - antisamy-markup-formatter:2.1
          - handlebars:1.1.1
          - pipeline-rest-api:2.14
          - jquery-detached:1.2.1
          - git-client:3.4.2
          - momentjs:1.1.1
          - jquery3-api:3.5.1-1
          - snakeyaml-api:1.26.4
          - durable-task:1.34
          - structs:1.20
          - pipeline-input-step:2.11
          - pipeline-build-step:2.13
          - script-security:1.74
          - pipeline-graph-analysis:1.10
          - scm-api:2.6.3
          - mailer:1.32
          - workflow-api:2.40
          - workflow-basic-steps:2.20
          - extended-read-permission:3.2
          - git:4.4.0
          - pipeline-stage-view:2.14
          - variant:1.3
          - cloudbees-disk-usage-simple:0.10
          - ldap:1.24
          - credentials:2.3.12
          - trilead-api:1.0.8
          - windows-slaves:1.6
          - pipeline-milestone-step:1.3.1
          - workflow-cps:2.82
          - jackson2-api:2.11.2
          - plugin-util-api:1.2.4
          - apache-httpcomponents-client-4-api:4.5.10-2.0
          - kubernetes-credentials:0.7.0
          - workflow-multibranch:2.22
          - pipeline-stage-tags-metadata:1.7.1
          - kubernetes-client-api:4.9.2-2
          - pam-auth:1.6
          - junit:1.32
          - pipeline-model-definition:1.7.1
          - matrix-auth:2.6.2
          - bouncycastle-api:2.18
          - kubernetes:1.27.0

        jenkins_ingressroute_dns: jenkins.devops-internal.sunat-cuc.int

        jenkins_ingressroute: |
          redirect_http_to_https: true
          match: "Host(`{{ jenkins_ingressroute_dns }}`)"
          tls:
            domains:
            - main: {{ jenkins_ingressroute_dns }}
            secretName: {{ ingress_certificate_secret }}

        jenkins_serviceAnnotations: |
          prometheus.io/path: /prometheus
          prometheus.io/port: "8080"
          prometheus.io/scheme: http
          prometheus.io/scrape: "true"

        jenkins_resources: |
          requests:
            cpu: "50m"
            memory: "256Mi"
          limits:
            cpu: "2000m"
            memory: "4096Mi"

        jenkins_enableXmlConfig: true
        jenkins_overwriteConfig: true
        jenkins_overwriteJobs: true

        jenkins_JCasC: |
          defaultConfig: true
          configScripts:
            custom: |
              unclassified:
                location:
                  adminAddress: "admin@example.com"
                  url: "https://{{ jenkins_ingressroute_dns }}"
                prometheusConfiguration:
                  collectingMetricsPeriodInSeconds: 120
                  countAbortedBuilds: true
                  countFailedBuilds: true
                  countNotBuiltBuilds: true
                  countSuccessfulBuilds: true
                  countUnstableBuilds: true
                  defaultNamespace: "default"
                  fetchTestResults: true
                  jobAttributeName: "jenkins_job"
                  path: "prometheus"
                  processingDisabledBuilds: true
                  useAuthenticatedEndpoint: false

        jenkins_jobsasxml: |
          testJenkinsJob: |-
            <?xml version='1.1' encoding='UTF-8'?>
            <flow-definition plugin="workflow-job@2.39">
              <description></description>
              <keepDependencies>false</keepDependencies>
              <properties/>
              <definition class="org.jenkinsci.plugins.workflow.cps.CpsFlowDefinition" plugin="workflow-cps@2.82">
                <script>podTemplate(label: &apos;jnlp-petclinic&apos;, slaveConnectTimeout: &apos;600&apos;, envVars: [envVar(key: &apos;GIT_BRANCH&apos;, value: &apos;master&apos;)], containers: [
                containerTemplate(
                        name: &apos;maven&apos;,
                        image: &apos;maven:3.6.3-jdk-11&apos;,
                        ttyEnabled: true,
                        resourceLimitCpu: &apos;1000m&apos;,
                        resourceLimitMemory: &apos;768Mi&apos;,
                        resourceRequestCpu: &apos;250m&apos;,
                        resourceRequestMemory: &apos;256Mi&apos;,
                        command: &apos;cat&apos;,
                        envVars: [
                        ]
                )
            ]
            ) {
                node(&apos;jnlp-petclinic&apos;) {
                    stage(&apos;Hello World&apos;) {
                        container(&apos;maven&apos;) {
                          stage(&apos;Hello from Jenkins&apos;) {
                              sh &apos;&apos;&apos;#!/bin/bash
                              echo Hello From Ephemeral Agent
                              &apos;&apos;&apos;
                          }
                        }
                    }
                  }
              }
            </script>
                <sandbox>true</sandbox>
              </definition>
              <triggers/>
              <disabled>false</disabled>
            </flow-definition>

  tasks:

````