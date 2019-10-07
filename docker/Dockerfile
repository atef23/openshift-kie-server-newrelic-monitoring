FROM image-registry.openshift-image-registry.svc:5000/kie-server-integrations-newrelic/rhpam73-kieserver-openshift:1.0

USER root
RUN mkdir /newrelic
ADD ./newrelic/newrelic.jar /newrelic/newrelic.jar
ADD ./newrelic/newrelic.yml /newrelic/newrelic.yml
RUN echo '# New Relic Agent' >> /opt/eap/bin/standalone.conf
RUN echo 'JAVA_OPTS="${JAVA_OPTS} -javaagent:/newrelic/newrelic.jar"' >> /opt/eap/bin/standalone.conf
USER jboss
