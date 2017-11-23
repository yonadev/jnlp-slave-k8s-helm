# Jenkins JNLP Agent Docker image with kubectl and helm

[![Docker Stars](https://img.shields.io/docker/stars/yonadev/jnlp-slave-k8s-helm.svg)](https://hub.docker.com/r/yonadev/jnlp-slave-k8s-helm/)
[![Docker Pulls](https://img.shields.io/docker/pulls/yonadev/jnlp-slave-k8s-helm.svg)](https://hub.docker.com/r/yonadev/jnlp-slave-k8s-helm/)
[![Docker Automated build](https://img.shields.io/docker/automated/yonadev/jnlp-slave.svg)](https://hub.docker.com/r/yonadev/jnlp-slave-k8s-helm/)

This image allows for a slave deployed into a k8s cluster to be able to run kubectl and helm commands

We use it for CD actions from Jenkins

Based on Jenkins official jnlp-slave image https://hub.docker.com/r/jenkinsci/jnlp-slave/
kubectl and helm are added in.

## Running

To run a Docker container

    docker run yonadev/jnlp-slave-k8s-helm -url http://jenkins-server:port <secret> <agent name>

To run a Docker container with [Work Directory](https://github.com/jenkinsci/remoting/blob/master/docs/workDir.md):

    docker run yonadev/jnlp-slave-k8s-helm -url http://jenkins-server:port -workDir=/home/jenkins/agent <secret> <agent name>

Optional environment variables:

* `JENKINS_URL`: url for the Jenkins server, can be used as a replacement to `-url` option, or to set alternate jenkins URL
* `JENKINS_TUNNEL`: (`HOST:PORT`) connect to this agent host and port instead of Jenkins server, assuming this one do route TCP traffic to Jenkins master. Useful when when Jenkins runs behind a load balancer, reverse proxy, etc.
* `JENKINS_SECRET`: agent secret, if not set as an argument
* `JENKINS_AGENT_NAME`: agent name, if not set as an argument

## Configuration specifics

### Enabled JNLP protocols

By default, the [JNLP3-connect](https://github.com/jenkinsci/remoting/blob/master/docs/protocols.md#jnlp3-connect) is disabled due to the known stability and scalability issues.
You can enable this protocol on your own risk using the 
`JNLP_PROTOCOL_OPTS=-Dorg.jenkinsci.remoting.engine.JnlpProtocol3.disabled=false` property (the protocol should be enabled on the master side as well).

In Jenkins versions starting from `2.27` there is a [JNLP4-connect](https://github.com/jenkinsci/remoting/blob/master/docs/protocols.md#jnlp4-connect) protocol. 
If you use Jenkins `2.32.x LTS`, it is recommended to enable the protocol on your instance.

### Amazon ECS

Make sure your ECS container agent is [updated](http://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-agent-update.html) before running. Older versions do not properly handle the entryPoint parameter. See the [entryPoint](http://docs.aws.amazon.com/AmazonECS/latest/developerguide/task_definition_parameters.html#container_definitions) definition for more information.
