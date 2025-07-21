## Software Template for Jenkins on Red Hat Developer Hub

[![Open in Cloud Shell](https://gstatic.com/cloudssh/images/open-btn.svg)](https://ssh.cloud.google.com/cloudshell/editor?cloudshell_git_repo=https://github.com/maximilianoPizarro/software-template-jenkins-rhdh&cloudshell_tutorial=README.md&cloudshell_workspace=/)

This repository contains a Backstage software template designed for creating new services that integrate with Jenkins for CI/CD. It's specifically tailored for use with Red Hat Developer Hub.

### Features

*   **Scaffolding:** Quickly generate new service repositories with pre-configured Jenkinsfile.
*   **Jenkins Integration:** Seamlessly connects with your existing Jenkins instance for automated builds and deployments.
*   **Red Hat Developer Hub Ready:** Designed to be easily imported and used within Red Hat Developer Hub.

### Prerequisites

Before using this template, ensure you have the following:

*   A running instance of [Red Hat Developer Hub](https://developers.redhat.com/products/developer-hub).
*   Access to a Jenkins instance.
*   Necessary credentials configured in Red Hat Developer Hub for Jenkins integration (e.g., Jenkins API token).

### Usage

1.  **Import the Template:**
    *   In Red Hat Developer Hub, navigate to the "Create" section.
    *   Select "Register Existing Component" or "Import Repository".
    *   Provide the URL to this GitHub repository: `https://github.com/maximilianoPizarro/software-template-jenkins-rhdh`

2.  **Create a New Service:**
    *   Once the template is imported, you can use it to scaffold new services.
    *   Go to the "Create" section in Red Hat Developer Hub and select this template.
    *   Follow the prompts to provide details for your new service (e.g., service name, repository URL).

3.  **Jenkinsfile:**
    The generated service will include a basic `Jenkinsfile` that you can customize to define your CI/CD pipeline. This file will typically include stages for:
    *   Building your application.
    *   Running tests.
    *   Deploying to your target environment.

### Customization

You can customize this template to fit your specific needs:

*   **Modify the `template.yaml`:** Adjust the input parameters and steps in the `template.yaml` file to control what information is collected from the user and how the new service is generated.
*   **Update the `Jenkinsfile`:** Enhance the default `Jenkinsfile` to include more complex CI/CD stages, integrate with other tools, or enforce specific build processes.
*   **Add more


### Developer Hub Configuration

To integrate this template with Red Hat Developer Hub, you'll need to configure the `dynamic-plugins.yaml` and `app-config-rhdh.yaml` files.

#### `dynamic-plugins.yaml`

This configuration enables the Jenkins backend and frontend plugins, allowing Developer Hub to interact with Jenkins and display Jenkins-related information.

```yaml
includes:
  - dynamic-plugins.default.yaml
plugins:
  - package: oci://quay.io/maximilianopizarro/scaffolder-backend-module-jenkins:v0.1!backstage-community-plugin-scaffolder-backend-module-jenkins
    disabled: false
  - disabled: false
    package: ./dynamic-plugins/dist/backstage-community-plugin-jenkins-backend-dynamic
  - disabled: false
    package: ./dynamic-plugins/dist/backstage-community-plugin-jenkins
    pluginConfig:
      dynamicPlugins:
        frontend:
          backstage-community.plugin-jenkins:
            mountPoints:
            - config:
                if:
                  allOf:
                  - isJenkinsAvailable
                layout:
                  gridColumn: 1 / -1
              importName: EntityJenkinsContent
              mountPoint: entity.page.ci/cards
```

#### `app-config-rhdh.yaml`

This file configures the Jenkins connection details for Developer Hub. Replace the placeholder values with your actual Jenkins server URL, username, and API key.

```yaml
jenkins:
  baseUrl: ${JENKINS_SERVER_URL}/createItem?mode=org.jenkinsci.plugins.workflow.job.WorkflowJob
  username: ${JENKINS_USERNAME}
  apiKey: ${JENKINS_API_KEY}
  crumbIssuerEnabled: false
  instances:
    - baseUrl: 'https://jenkins-jenkins.apps.rosa.m5cq8-ma8k4-7s6.ak3p.p3.openshiftapps.com' # Replace with your Jenkins instance URL
      name: 'default-jenkins'
      username: '${JENKINS_USERNAME}' # Replace with your Jenkins username
      apiKey: '${JENKINS_API_KEY}' # Replace with your Jenkins API key
```