Vagrant.configure("2") do |config|
    # Servidor Apache 1
    config.vm.define "web1" do |web1|
      web1.vm.box = "ubuntu/bionic64"
      web1.vm.network "private_network", ip: "192.168.33.10"
      web1.vm.provider "virtualbox" do |vb|
        vb.memory = "512"
        vb.cpus = 1
      end
      web1.vm.synced_folder "./web1", "/var/www/html"
      web1.vm.provision "shell", inline: <<-SHELL
        sudo apt update
        sudo apt install -y apache2
        sudo systemctl enable apache2
      SHELL
    end
  
    # Servidor Apache 2
    config.vm.define "web2" do |web2|
      web2.vm.box = "ubuntu/bionic64"
      web2.vm.network "private_network", ip: "192.168.33.11"
      web2.vm.provider "virtualbox" do |vb|
        vb.memory = "512"
        vb.cpus = 1
      end
      web2.vm.synced_folder "./web2", "/var/www/html"
      web2.vm.provision "shell", inline: <<-SHELL
        sudo apt update
        sudo apt install -y apache2
        sudo systemctl enable apache2
      SHELL
    end
  
    # Servidor Nginx
    config.vm.define "loadbalancer" do |lb|
      lb.vm.box = "ubuntu/bionic64"
      lb.vm.network "private_network", ip: "192.168.33.12"
      lb.vm.provider "virtualbox" do |vb|
        vb.memory = "512"
        vb.cpus = 1
      end
      lb.vm.provision "shell", inline: <<-SHELL
        sudo apt update
        sudo apt install -y nginx
        sudo rm /etc/nginx/sites-enabled/default
        echo '
        upstream backend {
            server 192.168.33.10;
            server 192.168.33.11;
        }
        server {
            listen 80;
            location / {
                proxy_pass http://backend;
            }
        }' | sudo tee /etc/nginx/sites-available/load_balancer
        sudo ln -s /etc/nginx/sites-available/load_balancer /etc/nginx/sites-enabled/
        sudo systemctl restart nginx
        sudo systemctl enable nginx
      SHELL
    end
  end
  