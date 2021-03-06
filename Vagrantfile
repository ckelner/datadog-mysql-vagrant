# -*- mode: ruby -*-
# vi: set ft=ruby :
# This script will bring up a vagrant with the datadog agent installed - it does
# not expect a local mysql instance running. See the README for an easy way to
# bring up a MySQL host. This was specific to my testing needs (remote MySQL DB)
# You will need to set DD_API_KEY when you run vagrant, i.e.
# `DD_API_KEY=<YOURAPIKEYHERE> vagrant up`

Vagrant.configure("2") do |config|
  # Image to use
  config.vm.box = "ubuntu/precise64"
  config.vm.box_url = "http://files.vagrantup.com/precise64.box"

  config.vm.provision "shell", inline: <<-SHELL
    echo "Provisioning..."
    echo "------------------------------------------"
    echo "Setting up Datadog..."
    # Get the API Key from the host environment
    APIKEY=#{ENV['DD_API_KEY']}
    # NOTE: Change you values below if you want something different
    TAGS='name:mysql-test, role:mysql-test, kelner:test'
    HOSTNAME='mysql-test-1'
    # Re-check to see if able to load the DD API Key
    if [[ $APIKEY != "" && ! -z $APIKEY ]]; then
      sudo apt-get update
      sudo apt-get install apt-transport-https
      sudo sh -c "echo 'deb https://apt.datadoghq.com/ stable main' > /etc/apt/sources.list.d/datadog.list"
      sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys C7A7DA52
      sudo apt-get update
      sudo apt-get install datadog-agent
      # Hacky but it works...
      sudo sh -c "sed 's/api_key:.*/api_key: $APIKEY/' /etc/dd-agent/datadog.conf.example > /etc/dd-agent/datadog.conf"
      sudo sh -c "sed 's/# tags:.*/tags: $TAGS/' /etc/dd-agent/datadog.conf > /etc/dd-agent/tmp.conf"
      sudo sh -c "sed 's/# hostname:.*/hostname: $HOSTNAME/' /etc/dd-agent/tmp.conf > /etc/dd-agent/temp.conf"
      sudo sh -c "mv /etc/dd-agent/temp.conf /etc/dd-agent/datadog.conf"
      # enable mysql
      echo "Enabling NFS Check"
      sudo sh -c "cp /etc/dd-agent/conf.d/mysql.yaml.example /etc/dd-agent/conf.d/mysql.yaml"
      sudo /etc/init.d/datadog-agent start
    else
      echo "No DD_API_KEY set!! Cannot setup Datadog. Please see the README.md."
    fi
    echo "------------------------------------------"
    echo "Provisioning Complete!"
  SHELL
end
