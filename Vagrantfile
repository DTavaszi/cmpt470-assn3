# -*- mode: ruby -*-
# vi: set ft=ruby :
Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/xenial64"

  config.vm.network "forwarded_port", guest: 3000, host: 3000

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.

  config.vm.provision "shell", inline: <<-SHELL
    echo "-------------------- updating package lists"
    apt-get -y update
    # gotta put this before the upgrade, b/c it reboots and then all commands are lost
    echo "-------------------- installing postgres"
    apt-get -y install postgresql
    apt-get -y install libpq-dev
    echo "-------------------- creating postgres vagrant role with password vagrant"
    # Create Role and login
    sudo su postgres -c "psql -c \"CREATE ROLE vagrant SUPERUSER LOGIN PASSWORD 'vagrant'\""
    echo "-------------------- creating wtm database"
    # Create WTM database
    sudo su postgres -c "createdb -E UTF8 -T template0 --locale=en_US.utf8 -O vagrant wtm"
    echo "-------------------- upgrading packages to latest"
    apt-get -y upgrade
    apt-get -y autoremove

    sudo apt-get install -y curl gnupg gpgv2 gnupg2

    # rbenv prerequisites
    sudo apt-get -y install autoconf bison build-essential libssl-dev libyaml-dev libreadline6-dev zlib1g-dev libncurses5-dev libffi-dev libgdbm3 libgdbm-dev

    # install git
    sudo apt-get -y install git

    # install rbenv
    # git clone https://github.com/rbenv/rbenv.git ~/.rbenv
    sudo apt-get -y install rbenv ruby-build

    # add rbenv to $PATH
    echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bashrc
    echo 'eval "$(rbenv init -)"' >> ~/.bashrc
  end

  config.vm.provision "shell", inline: <<-SHELL
    # add 'install' option to rbenv
    git clone https://github.com/rbenv/ruby-build.git "$(rbenv root)"/plugins/ruby-build

    # install ruby
    rbenv install --verbose 2.5.0
    rbenv global 2.5.0

    # rails
    sudo gem install rails

    # working directory
    cd vagrant

    # bundler for gems
    sudo apt-get -y install bundler
    bundle

    # nodejs is a dependency for running the server
    curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash
    sudo apt-get -y install nodejs

    # heroku
    wget -qO- https://cli-assets.heroku.com/install-ubuntu.sh | sh
  SHELL
end
