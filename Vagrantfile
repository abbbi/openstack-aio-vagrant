# -*- mode: ruby -*-
# vi: set ft=ruby :
# https://docs.openstack.org/openstack-ansible/latest/user/aio/quickstart.html

Vagrant.configure("2") do |config|
  config.vm.box = "generic/debian11"

  config.vm.provider :libvirt do |domain|
    domain.storage :file, :size => "250G", :pool => "cache"
    domain.memory = 16000
    domain.cpus = 16
    domain.nested = true
  end

  config.vm.provision "shell", inline: <<-SHELL
     apt-get update -y
     apt-get install -qy git
     git clone https://opendev.org/openstack/openstack-ansible /opt/openstack-ansible
     cd /opt/openstack-ansible
     git checkout master
     scripts/bootstrap-ansible.sh

    export BOOTSTRAP_OPTS="bootstrap_host_data_disk_device=vdb"
    export BOOTSTRAP_OPTS="${BOOTSTRAP_OPTS} bootstrap_host_data_disk_fs_type=xfs"
    export SCENARIO='aio_lxc_barbican_ceph_lxb'
    scripts/bootstrap-aio.sh

    cd /opt/openstack-ansible/playbooks
    openstack-ansible setup-hosts.yml
    openstack-ansible setup-infrastructure.yml
    openstack-ansible setup-openstack.yml
    openstack-ansible os-keystone-install.yml
    grep admin_pass /etc/openstack_deploy/user_secrets.yml
   SHELL
end
