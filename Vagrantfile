# -*- mode: ruby -*-
# vi: set ft=ruby :

BASE_BOX = ENV['BI_VAGRANT_BOX'] || 'precise64'
VAGRANT_BOX_URL = ENV['BI_VAGRANT_URl'] || "http://files.vagrantup.com/#{BASE_BOX}.box"
CHEF_PATH = ENV['BI_CHEF_PATH'] || '~/.kitchen'		#path to the believe chef-repo
USER = ENV['BI_CHEF_USER'] || ENV['USER']
CLIENT = "believein"
PROJECT = "willie"

Vagrant::Config.run do |config|

  config.vm.box = BASE_BOX
  config.vm.box_url = VAGRANT_BOX_URL
  config.vm.host_name = "dev.believe.in"
  #config.ssh.private_key_path = "~/.ssh/id_rsa"

  # Do not Change - Folder shareing
  config.vm.share_folder("v-root", "/believein/data/www/#{PROJECT}", ".", :nfs => true)
  config.vm.customize ["setextradata", :id, "VBoxInternal2/SharedFoldersEnableSymlinksCreate/v-root", "1"]

  config.vm.network :hostonly, "192.168.100.101"
  config.vm.forward_port 9000, 8080

  config.vm.provision :chef_solo do |chef|

    chef.node_name = "#{USER}-vagrant-#{PROJECT}"
    chef.cookbooks_path = ["#{CHEF_PATH}/cookbooks"]
    chef.roles_path = "#{CHEF_PATH}/roles/"
    chef.data_bags_path = "#{CHEF_PATH}/data_bags/"
    chef.run_list = [
    	"apt",
    	"python",
    	"believein-base",
    	"believein-base::directories",
		"believein-platform",
    ]
    chef.provisioning_path = "/etc/chef"
    chef.log_level = :debug
    chef.json = {
    	:base => {
    		:user => "vagrant",
    		:group => "vagrant"
    	},
    	:believein_platform => {
    		:python => {
				:create_venv => "true",
				:virtual_env_name => "believein-irc",
				:virtual_env_dir => "/believein/data/python-virtualenvs/",
			},
    	},
    	:authorization => {
			:sudo => {
				:groups => ["admin", "sysadmin"],
				:users => ["vagrant"],
				:passwordless => true
			}
    	}
    }
  end
end
