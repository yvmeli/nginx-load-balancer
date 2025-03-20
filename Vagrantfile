# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  # Configuración común para todas las VMs
  config.vm.box = "ubuntu/focal64"
  config.vm.synced_folder ".", "/vagrant", disabled: false
  
  # Primer servidor web Apache
  config.vm.define "web1" do |web1|
    web1.vm.hostname = "web1"
    web1.vm.network "private_network", ip: "192.168.56.101"
    
    web1.vm.provider "virtualbox" do |vb|
      vb.memory = "512"
      vb.cpus = 1
      vb.name = "web1"
    end
    
    web1.vm.synced_folder "./web_content", "/var/www/html", create: true
    
    web1.vm.provision "shell", inline: <<-SHELL
      apt-get update
      apt-get install -y apache2
      echo "<h1>Servidor Web 1</h1><p>IP: 192.168.56.101</p>" > /var/www/html/index.html
      systemctl enable apache2
      systemctl restart apache2
    SHELL
  end
  
  # Segundo servidor web Apache
  config.vm.define "web2" do |web2|
    web2.vm.hostname = "web2"
    web2.vm.network "private_network", ip: "192.168.56.102"
    
    web2.vm.provider "virtualbox" do |vb|
      vb.memory = "512"
      vb.cpus = 1
      vb.name = "web2"
    end
    
    web2.vm.synced_folder "./web_content", "/var/www/html", create: true
    
    web2.vm.provision "shell", inline: <<-SHELL
      apt-get update
      apt-get install -y apache2
      echo "<h1>Servidor Web 2</h1><p>IP: 192.168.56.102</p>" > /var/www/html/index.html
      systemctl enable apache2
      systemctl restart apache2
    SHELL
  end
  
  # Servidor Nginx para balanceo de carga
  config.vm.define "lb" do |lb|
    lb.vm.hostname = "lb"
    lb.vm.network "private_network", ip: "192.168.56.100"
    # Puerto para acceder al balanceador desde el host
    lb.vm.network "forwarded_port", guest: 80, host: 8080
    
    lb.vm.provider "virtualbox" do |vb|
      vb.memory = "512"
      vb.cpus = 1
      vb.name = "lb"
    end
    
    lb.vm.provision "shell", inline: <<-SHELL
      apt-get update
      apt-get install -y nginx
      
      # Configuración de Nginx para balanceo de carga
      cat > /etc/nginx/sites-available/load-balancer << 'EOF'
upstream backend {
    server 192.168.56.101;  # Servidor web 1
    server 192.168.56.102;  # Servidor web 2
}

server {
    listen 80;
    
    location / {
        proxy_pass http://backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
EOF
      
      # Habilitar la configuración y reiniciar Nginx
      ln -sf /etc/nginx/sites-available/load-balancer /etc/nginx/sites-enabled/
      rm -f /etc/nginx/sites-enabled/default
      systemctl restart nginx
      
      echo "<h1>Balanceador de carga Nginx</h1><p>Este servidor distribuye el tráfico entre web1 y web2</p>" > /var/www/html/index.html
    SHELL
  end
end