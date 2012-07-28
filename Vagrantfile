# Gleefully borrowed from playdoh

# The Django project creation process only updates *.py files, so you will need
# to update project names, paths in this document

require "yaml"

# Load up our vagrant config files -- vagrantconfig.yaml first
_config = YAML.load(File.open(File.join(File.dirname(__FILE__),
                    "vagrantconfig.yaml"), File::RDONLY).read)

# Local-specific/not-git-managed config -- vagrantconfig_local.yaml
begin
    _config.merge!(YAML.load(File.open(File.join(File.dirname(__FILE__),
                   "vagrantconfig_local.yaml"), File::RDONLY).read))
rescue Errno::ENOENT # No vagrantconfig_local.yaml found -- that's OK; just
                     # use the defaults.
end

CONF = _config
MOUNT_POINT = '/var/apps/{{ project_name }}/source'

Vagrant::Config.run do |config|
    config.vm.box = "precise64"
    config.vm.box_url = "http://files.vagrantup.com/precise64.box"

    config.vm.forward_port 80, 8080, :auto => true # Nginx
    config.vm.forward_port 8000, 8888, :auto => true # Django dev server
    config.vm.forward_port 5000, 5555, :auto => true # Foreman/Honcho port
    config.vm.forward_port 5432, 5480, :auto => true # PostgreSQL
    config.vm.forward_port 22, 2222, :auto => true

    # Increase vagrant's patience during hang-y CentOS bootup
    # see: https://github.com/jedi4ever/veewee/issues/14
    config.ssh.max_tries = 50
    config.ssh.timeout   = 300

    if CONF['nfs'] == false or RUBY_PLATFORM =~ /mswin(32|64)/
        config.vm.share_folder("app_root", MOUNT_POINT, ".")
    else
        config.vm.share_folder("app_root", MOUNT_POINT, ".", :nfs => true)
    end

    # Add to /etc/hosts: 33.33.33.24 dev.playdoh.org
    config.vm.network :hostonly, "33.33.33.24"

    #config.vm.provision :puppet do |puppet|
    #    puppet.manifests_path = "puppet/manifests"
    #    puppet.manifest_file  = "dev.pp"
    #    puppet.module_path = "puppet/modules"
    #    options = ["--environment", "dev"]
    #end
end
