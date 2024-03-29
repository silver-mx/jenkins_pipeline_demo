Vagrant.configure("2") do |config|
  config.vm.box = "bento/centos-7.6"
  
  # Port for Jenkins
  config.vm.network "forwarded_port", guest: 8080, host: 8888

  config.vm.provider "virtualbox" do |vbox|
      vbox.customize ["modifyvm", :id, "--memory", "4096", "--ioapic", "on", "--cpus", "2", "--vram", "10"]
   end

  config.vbguest.no_install = true

  config.vm.network "public_network", bridge: "wlp1s0"

  config.vm.define "jenkins" do |c|
    c.vm.network :private_network, virtualbox__intnet: true, ip: "192.168.111.25"
    c.vm.hostname = "centos-vm"
  end

  config.vm.provision "shell", inline: <<-SHELL
    
  # TODO: Change keyboard to swedish
    sudo echo "LANG=en_US.UTF-8" >> /etc/environment
    sudo echo "LANGUAGE=en_US.UTF-8" >> /etc/environment
    sudo echo "LC_ALL=en_US.UTF-8" >> /etc/environment
    sudo echo "LC_CTYPE=en_US.UTF-8" >> /etc/environment
    
    # utilities
    sudo yum -y install curl
    sudo yum -y install wget
    # It is an old GIT version, see to update it
    sudo yum -y install zip
    sudo yum -y install unzip
    # GIT (CentOS comes with an old version)
    sudo yum -y install  https://centos7.iuscommunity.org/ius-release.rpm
    sudo yum -y install  git2u-all
    # install java
    curl -O https://download.java.net/java/GA/jdk11/9/GPL/openjdk-11.0.2_linux-x64_bin.tar.gz
    tar zxvf openjdk-11.0.2_linux-x64_bin.tar.gz
    sudo mv jdk-11.0.2/ /usr/local/
    echo -e '\nexport JAVA_HOME=/usr/local/jdk-11.0.2\n' >> /etc/profile.d/jdk11.sh
    echo -e '\nexport PATH=$PATH:$JAVA_HOME/bin\n' >> /etc/profile.d/jdk11.sh
    source /etc/profile.d/jdk11.sh
    # install docker
    sudo yum -y install -y yum-utils device-mapper-persistent-data lvm2
    sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
    sudo yum -y install docker-ce docker-ce-cli containerd.io
    sudo systemctl start docker
    sudo usermod -aG docker vagrant
    sudo systemctl enable docker
    
    # config jenkins
    sudo yum -y install fontconfig urw-fonts
    #mkdir -p ~/sw/jenkins
    #wget http://mirrors.jenkins.io/war-stable/latest/jenkins.war -O ~/sw/jenkins/jenkins.war
    #java -jar ~/sw/jenkins/jenkins.war --httpPort=8080
    #3cf02262c6894511ae6df83ff563dd13
  SHELL

  config.vm.provision "docker" do |d|
    d.run "jenkins/jenkins:lts",
      args: "-d -v jenkins_home:/var/jenkins_home -p 8080:8080 -p 50000:50000 --restart unless-stopped --name jenkins"
    d.run "sonatype/nexus3",
      args: "-d -p 8081:8081 -p 5000:5000 --name nexus -v nexus-data:/nexus-data --restart unless-stopped"
  end
end
