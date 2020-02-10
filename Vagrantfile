default_box = 'ubuntu/bionic64'

Vagrant.configure(2) do |config|
  config.vm.define 'm1' do |m1|
    m1.vm.box = default_box
    m1.vm.hostname = "m1"
    m1.vm.synced_folder ".", "/vagrant", type:"virtualbox"
    m1.vm.network 'private_network', ip: "192.168.0.200",  virtualbox__intnet: true
    m1.vm.network "forwarded_port", guest: 22, host: 2222, id: "ssh", disabled: true
    m1.vm.network "forwarded_port", guest: 22, host: 2000 # SSH TO m1/NODE
    m1.vm.network "forwarded_port", guest: 6443, host: 1234 # ACCESS K8S API
    for p in 30000..30100 # PORTS DEFINED FOR K8S TYPE-NODE-PORT ACCESS
      m1.vm.network "forwarded_port", guest: p, host: p, protocol: "tcp"
      end
    m1.vm.provider "virtualbox" do |v|
      v.memory = "1024"
      v.name = "m1"
      end

    m1.vm.provision "shell", inline: <<-SHELL
      sudo apt update
      sudo apt install etcd-server etcd-client -y
      IPADDR=$(ip a show enp0s8 | grep "inet " | awk '{print $2}' | cut -d / -f1)
      curl -sfL https://get.k3s.io | INSTALL_K3S_EXEC="--node-ip=${IPADDR} --flannel-iface=enp0s8 --write-kubeconfig-mode 644 --kube-apiserver-arg="service-node-port-range=30000-30100" --no-deploy=servicelb --no-deploy=traefik" K3S_STORAGE_BACKEND=etcd3 K3S_STORAGE_ENDPOINT="http://127.0.0.1:2379" sh -
      hostnamectl set-hostname m1
      NODE_TOKEN="/var/lib/rancher/k3s/server/node-token"
      while [ ! -e ${NODE_TOKEN} ]
      do
          sleep 1
      done
      cat ${NODE_TOKEN}
      cp ${NODE_TOKEN} /vagrant/
      sudo sed -i 's/ChallengeResponseAuthentication no/ChallengeResponseAuthentication yes/g' /etc/ssh/sshd_config
      sudo systemctl reload ssh
      KUBE_CONFIG="/etc/rancher/k3s/k3s.yaml"
      cp ${KUBE_CONFIG} /vagrant/ #copy contents of "k3s.yaml" to ".kube/config" to 'kubectl' from local-machine
    SHELL
  end

  config.vm.define 'n1' do |n1|
    n1.vm.box = default_box
    n1.vm.hostname = "n1"
    n1.vm.synced_folder ".", "/vagrant", type:"virtualbox"
    n1.vm.network 'private_network', ip: "192.168.0.201",  virtualbox__intnet: true
    n1.vm.network "forwarded_port", guest: 22, host: 2222, id: "ssh", disabled: true
    n1.vm.network "forwarded_port", guest: 22, host: 2001
    n1.vm.provider "virtualbox" do |v|
      v.memory = "1024"
      v.name = "n1"
      end
    
    n1.vm.provision "shell", inline: <<-SHELL
      IPADDR=$(ip a show enp0s8 | grep "inet " | awk '{print $2}' | cut -d / -f1)
      curl -sfL https://get.k3s.io | INSTALL_K3S_EXEC="--node-ip=${IPADDR} --flannel-iface=enp0s8" K3S_URL=https://192.168.0.200:6443 K3S_TOKEN=$(cat /vagrant/node-token) sh -
      sudo sed -i 's/ChallengeResponseAuthentication no/ChallengeResponseAuthentication yes/g' /etc/ssh/sshd_config
      sudo systemctl reload ssh
    SHELL
  end

  config.vm.define 'n2' do |n2|
    n2.vm.box = default_box
    n2.vm.hostname = "n2"
    n2.vm.synced_folder ".", "/vagrant", type:"virtualbox"
    n2.vm.network 'private_network', ip: "192.168.0.202",  virtualbox__intnet: true
    n2.vm.network "forwarded_port", guest: 22, host: 2222, id: "ssh", disabled: true
    n2.vm.network "forwarded_port", guest: 22, host: 2002
    n2.vm.provider "virtualbox" do |v|
      v.memory = "1024"
      v.name = "n2"
      end
    
    n2.vm.provision "shell", inline: <<-SHELL
      IPADDR=$(ip a show enp0s8 | grep "inet " | awk '{print $2}' | cut -d / -f1)
      curl -sfL https://get.k3s.io | INSTALL_K3S_EXEC="--node-ip=${IPADDR} --flannel-iface=enp0s8" K3S_URL=https://192.168.0.200:6443 K3S_TOKEN=$(cat /vagrant/node-token) sh -
      rm -f /vagrant/node-token
      sudo sed -i 's/ChallengeResponseAuthentication no/ChallengeResponseAuthentication yes/g' /etc/ssh/sshd_config
      sudo systemctl reload ssh
    SHELL
  end

end
