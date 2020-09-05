Vagrant.require_version ">= 2.2", "< 2.3"

unless Vagrant.has_plugin?("vagrant-disksize")
  raise 'vagrant-disksize is not installed!'
end

unless Vagrant.has_plugin?("vagrant-hostsupdater")
  raise 'vagrant-hostsupdater is not installed!'
end

Vagrant.configure("2") do |config|
  config.vm.box = "centos/8"
  config.vm.box_version = "1905.1"
  config.disksize.size = '50GB'

  config.vm.provision "shell" do |s|
    ssh_pub_key = File.readlines("#{Dir.home}/.ssh/id_rsa.pub").first.strip
    s.inline = <<-SHELL
      mkdir -p /root/.ssh/
      touch /root/.ssh/authorized_keys
      echo #{ssh_pub_key} >> /home/vagrant/.ssh/authorized_keys
      echo #{ssh_pub_key} >> /root/.ssh/authorized_keys
    SHELL
  end

  {
    'vm1' => '192.168.33.10',
    'vm2' => '192.168.33.11',
  }.each do |short_name, ip|
    config.vm.define short_name do |host|
      host.vm.network 'private_network', ip: ip
      host.vm.hostname = "#{short_name}.myapp.dev"
    end
  end
end
