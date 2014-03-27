#▐▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▌
#▐ ██╗   ██╗ █████╗  ██████╗ ██████╗  █████╗ ███╗   ██╗████████╗███████╗██╗██╗     ███████╗ ▌
#▐ ██║   ██║██╔══██╗██╔════╝ ██╔══██╗██╔══██╗████╗  ██║╚══██╔══╝██╔════╝██║██║     ██╔════╝ ▌
#▐ ██║   ██║███████║██║  ███╗██████╔╝███████║██╔██╗ ██║   ██║   █████╗  ██║██║     █████╗   ▌
#▐ ╚██╗ ██╔╝██╔══██║██║   ██║██╔══██╗██╔══██║██║╚██╗██║   ██║   ██╔══╝  ██║██║     ██╔══╝   ▌
#▐  ╚████╔╝ ██║  ██║╚██████╔╝██║  ██║██║  ██║██║ ╚████║   ██║   ██║     ██║███████╗███████╗ ▌
#▐   ╚═══╝  ╚═╝  ╚═╝ ╚═════╝ ╚═╝  ╚═╝╚═╝  ╚═╝╚═╝  ╚═══╝   ╚═╝   ╚═╝     ╚═╝╚══════╝╚══════╝ ▌
#▐▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▌

#############################################################################################
# Configuration                                                                             #
#############################################################################################

$ubuntu_url      = "http://cloud-images.ubuntu.com/vagrant/saucy/current/saucy-server-cloudimg-amd64-vagrant-disk1.box"
$hostname        = "development"
$box_name        = "ubuntu-13.10"
$cpus            = 2
$memory          = 2056
$port_forward    = { 8000 => 8000, 5000 => 5000, 3000 => 3000 }
$sync_folders    = {
                     '/Users/briandavidwetzel/Documents' => '/home/vagrant/Documents',
                     '/Users/briandavidwetzel/Downloads' => '/home/vagrant/Downloads',
                     '/Users/briandavidwetzel/code'      => '/home/vagrant/code',
                     '/Users/briandavidwetzel/.ssh/dev'  => '/home/vagrant/.ssh'
                   }
$rubies          = ['1.9.3-p484', '2.0.0-p448']
$provision_tasks = [
                     :disable_prompt,
                     :configure_timezone,
                     :install_packages,
                     :configure_zsh,
                     :install_rbenv,
                     :get_configuration_files,
                     :install_powerline,
                     :configure_postgres,
                     :install_heroku_toolbelt,
                     :chown_home_dir #DO THIS LAST
                   ]
$with_powerline  = true

#############################################################################################
# Helper Methods                                                                            #
#############################################################################################
def comment_banner(msg)
  banner = ""
  banner << "echo '########################################'\n"
  banner << "echo '#{msg}...'\n"
  banner << "echo '########################################'\n"
end

def clone_into(repo, target)
  "git clone https://github.com/#{repo} #{target}\n"
end

def change_dir(dir)
  p  = comment_banner "Changing to Directory: #{dir}"
  p << "cd #{dir}\n"
end

def create_dir(dir)
  p  = comment_banner "Creating Directory: #{dir}"
  p << "mkdir #{dir}\n"
end

$home_directory   = '/home/vagrant'
$config_directory = "#{$home_directory}/.config"

#############################################################################################
# Provisioning Tasks                                                                        #
#############################################################################################

def disable_prompt
  "export DEBIAN_FRONTEND=noninteractive\n"
end

def configure_timezone
  p  = comment_banner "Configure Timezone"
  p << "locale-gen en_US.UTF-8\n"
  p << "ln -sf /usr/share/zoneinfo/EST /etc/localtime\n"
end

def install_packages
  p  = comment_banner "Install Packages"
  p << "add-apt-repository 'deb http://apt.postgresql.org/pub/repos/apt/ saucy-pgdg main'\n"
  p << "wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -\n"
  p << "apt-get update\n"
  p << "apt-get install -q -y tmux vim zsh htop git imagemagick graphviz nodejs postgresql-9.3 postgresql-server-dev-9.3 libssl-dev libxslt-dev libxml2-dev libreadline-dev python-pip exuberant-ctags\n"
end

def configure_zsh
  p  = comment_banner "Configure Zsh"
  p << clone_into('robbyrussell/oh-my-zsh', "#{$home_directory}/.oh-my-zsh")
  p << "chsh -s $(which zsh) vagrant\n"
end

def install_heroku_toolbelt
  p  = comment_banner 'Install Heroku Toolbelt'
  p << "wget -qO- https://toolbelt.heroku.com/install-ubuntu.sh | sh\n"
end

def install_rbenv
  p  = comment_banner 'Install Rbenv'
  p << clone_into('sstephenson/rbenv', "#{$home_directory}/.rbenv")
  p << clone_into('sstephenson/ruby-build', "#{$home_directory}/.rbenv/plugins/ruby-build")
end

def get_configuration_files
  p  = comment_banner 'Get Configuration Files'
  p << clone_into('briandavidwetzel/dotconfig', $config_directory)

  if $with_powerline
    p << change_dir($config_directory)
    p << "git checkout with-powerline\n"
  end

  p << "sh #{$config_directory}/init.sh #{$home_directory}\n"
  p << "sh #{$config_directory}/pathogen.sh #{$home_directory}\n"
end

def configure_postgres
  p  = comment_banner 'Configure Postgres'
  p << "su -c 'createuser vagrant -s' postgres\n"
  p << change_dir('/etc/postgresql/9.3/main/')
  p << "rm pg_hba.conf\n"
  p << "wget https://raw.github.com/briandavidwetzel/dotconfig/master/pg_hba.conf\n"
  p << "service postgresql restart\n"
end

def install_powerline
  p  = comment_banner 'Install Powerline'
  p << clone_into('Lokaltog/powerline', "#{$home_directory}/powerline")
  p << "pip install git+git://github.com/Lokaltog/powerline\n"
end

def create_code_dir
  create_dir("#{$home_directory}/code")
end

def chown_home_dir
  p  = comment_banner 'Chown Home Directory'
  p << "chown -R vagrant:vagrant #{$home_directory}\n"
end

def build_inline_provision
  $provision  = comment_banner("Provisioning")

  $provision_tasks.each do |t|
    $provision << send(t)
  end

  $provision << comment_banner("Provisioning Complete")
end

build_inline_provision

#############################################################################################
# Vagrant Definitions                                                                       #
#############################################################################################

Vagrant.configure("2") do |config|

  config.vm.define :devbox do |dev|
    dev.vm.box      = $box_name
    dev.vm.hostname = $hostname
    dev.vm.box_url  = $ubuntu_url

    dev.vm.provider "virtualbox" do |v|
        v.memory = $memory
        v.customize ["modifyvm", :id, "--ioapic", "on"]
        v.customize ["modifyvm", :id, "--cpus", $cpus]
    end

    $port_forward.each do |k,v|
      dev.vm.network :forwarded_port, :guest => k, :host => v
    end

    $sync_folders.each do |k,v|
      dev.vm.synced_folder k, v
    end

    dev.vm.provision :shell, inline: $provision
  end

end
