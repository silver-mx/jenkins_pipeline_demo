# jenkins_pipeline_demo
Jenkins pipeline demo

```
vagrant up

# Once up do:

mkdir -p ~/sw/jenkins
wget http://mirrors.jenkins.io/war-stable/latest/jenkins.war -O ~/sw/jenkins/jenkins.war
java -jar ~/sw/jenkins/jenkins.war --httpPort=8080

```

From the Virtualbox host open `http://localhost:8888`
