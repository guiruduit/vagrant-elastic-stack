Vagrant.configure("2") do |config|

  config.vm.define "elastic" do |el|
    el.vm.box_check_update = false
    el.vm.box = "debian/buster64"
    el.vm.network "private_network", ip: "172.27.11.10"

    el.vm.provider "virtualbox" do |vb|
      vb.memory = 2048
      vb.cpus = 2
    end

    el.vm.provision "shell", inline: <<-SHELL
      sudo apt update
      sudo apt install -y default-jre default-jdk docker.io docker-compose
      git clone https://github.com/deviantony/docker-elk.git
      cd docker-elk
      docker-compose up -d
    SHELL
  end

  config.vm.define "host1" do |h1|
    h1.vm.box_check_update = false
    h1.vm.box = "debian/buster64"
    h1.vm.hostname = "host1"
    h1.vm.network "private_network", ip: "172.27.11.100"

    h1.vm.provision "file", 
      source: "./filebeat.yml",
      destination: "/tmp/filebeat.yml"

    h1.vm.provision "shell", inline: <<-SHELL
      sudo -i

      curl -L -O https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-7.12.0-amd64.deb
      dpkg -i filebeat-7.12.0-amd64.deb

      chown root:root /tmp/filebeat.yml
      mv /tmp/filebeat.yml /etc/filebeat/filebeat.yml

      filebeat modules enable system
      filebeat setup -e

      systemctl enable filebeat
      systemctl start filebeat
    SHELL
  end
end
