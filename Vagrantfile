# -*- mode: ruby -*-
# vi: set ft=ruby :

# Install vagrant-list for the ability to list running vagrant boxes
# https://github.com/joshmcarthur/vagrant-list
# vagrant plugin install vagrant-list

# Install vagrant-hostsupdater to automatically add hosts entries
# https://github.com/cogitatio/vagrant-hostsupdater
# vagrant plugin install vagrant-hostsupdater

Vagrant::configure("2") do |config|
  config.vm.box = "Debian Wheezy 7.0 amd64 (Minimal Install + Puppet)"
  config.vm.box_url = "https://dropbox.com/s/23gupgb0xompvkm/Wheezy64.box?dl=1"

  load File.expand_path("./user/directories.pp")
  config.vm.synced_folder $projects, "/srv/www/", :nfs => true
  config.vm.synced_folder "./configuration/", "/configuration/", :nfs => true

  config.vm.network :private_network,
    ip: "192.168.54.54"

  config.vm.hostname = "www.bpb-vagrant-php-54.dev"

  load File.expand_path("../user/vhosts.pp", __FILE__)
  config.hostsupdater.aliases = $vhosts.keys
  config.hostsupdater.remove_on_suspend = true

  config.vm.provider :virtualbox do |vb|
    vb.gui = false
    vb.customize [
      "modifyvm", :id,
      "--memory", "1024",
      "--name", "bpb-php-54",
      "--natdnshostresolver1", "on"
    ]
  end

  # Prepare facts
  $facts = "export FACTER_vagrant_apache_vhosts=\""
  $vhosts.each do |name, directory|
    $facts += name + "," + directory + " "
  end
  $facts += "\";\n"

  load File.expand_path("../user/dbs.pp", __FILE__)
  $facts += "export FACTER_dbs=\""
  $dbs.each do |name, username_password|
    $facts += name + "," + username_password + " "
  end
  $facts += "\";\n"

  $bootstrap = File.read("./shell/bootstrap.sh")

  config.vm.provision :shell, :inline => $facts + $bootstrap;
end
