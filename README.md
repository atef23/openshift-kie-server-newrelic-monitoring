# Openshift KIE Server New Relic Monitoring
This guide provides setup instructions for implementing JVM-level monitoring on Openshift deployed kie-server pods using New Relic. This setup starts with a base kie-server openshift image. It then creates and deploys a custom image with New Relic assets set up for monitoring.

### Requirements
- New Relic subscription and license key
- Openshift cluster with base kie-server images or access to pull images from Red Hat Registry

### Setup

#### Update Assets

- Update /docker/Dockerfile to point to your base kie-server image. The line below points to a base rhpam73-kieserver-openshift image which has been pulled into the current openshift project from Red Hat Registry. If you are using a different image, point to where that image is located.

```bash
FROM image-registry.openshift-image-registry.svc:5000/kie-server-integrations-newrelic/rhpam73-kieserver-openshift:1.0

```
- Update /docker/newrelic/newrelic.yml and add your New Relic license key to the file (https://docs.newrelic.com/docs/accounts/install-new-relic/account-setup/license-key).

#### Create and Deploy New Relic kie-server Image
- Run the following comands to create a custom image and deploy it in your openshift project:
```bash
oc new-project kie-server-integrations-newrelic
oc create secret generic kieserver-app-secret --from-file=keystore.jks
oc new-build --strategy=docker --binary --name custom-kieserver
oc start-build custom-kieserver --from-dir=/docker
oc new-app -f rhpam73-prod-immutable-kieserver-newrelic.yaml -p KIE_SERVER_HTTPS_SECRET=kieserver-app-secret -p SOURCE_REPOSITORY_URL=https://github.com/jboss-container-images/rhpam-7-openshift-image.git -p MAVEN_REPO_URL=https://atefkieintegrations.jfrog.io/atefkieintegrations/kie-integrations-repo -p MAVEN_REPO_USERNAME=user -p MAVEN_REPO_PASSWORD=password -p KIE_SERVER_IMAGE_STREAM_NAME=custom-kieserver -p APPLICATION_NAME=custom
```
- The new kie-server should be deployed with New Relic monitored enabled. Wait up to 30 minutes to allow monitoring data to show up your New Relic dashboard.
