Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/jammy64"

  nodes = { "rmq01" => "192.168.56.10", "rmq02" => "192.168.56.11" }

  nodes.each do |name, ip|
    config.vm.define name do |node|
      node.vm.hostname = name
      node.vm.network "private_network", ip: ip
      node.vm.provider "virtualbox" do |vb|
        vb.name = name
        vb.memory = 1024
        vb.cpus = 1
      end
      node.vm.provision "shell", inline: <<-SHELL
        echo "192.168.56.10 rmq01" >> /etc/hosts
        echo "192.168.56.11 rmq02" >> /etc/hosts
        sed -i "/127.0.1.1/d;/127.0.2.1/d" /etc/hosts
        apt-get update -qq
        DEBIAN_FRONTEND=noninteractive apt-get install -y -qq rabbitmq-server python3-pika
        echo "SHUSHNOVRMQCOOKIE" > /var/lib/rabbitmq/.erlang.cookie
        chown rabbitmq:rabbitmq /var/lib/rabbitmq/.erlang.cookie
        chmod 400 /var/lib/rabbitmq/.erlang.cookie
        systemctl restart rabbitmq-server
        rabbitmq-plugins enable rabbitmq_management
        rabbitmqctl add_user admin admin || true
        rabbitmqctl set_user_tags admin administrator
        rabbitmqctl set_permissions -p / admin ".*" ".*" ".*"
      SHELL
    end
  end
end
