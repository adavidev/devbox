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
$cpus            = 3
$memory          = 3056
$port_forward    = { 8000 => 8000, 5000 => 5000, 3000 => 3000 }
$sync_folders    = {
                     '/Users/briandavidwetzel/Documents'          => '/home/vagrant/Documents',
                     '/Users/briandavidwetzel/Downloads'          => '/home/vagrant/Downloads',
                     "/Users/briandavidwetzel/code/#{$hostname}"  => '/home/vagrant/code'
                     #"/Users/briandavidwetzel/.ssh/#{$hostname}"  => '/home/vagrant/.ssh'
                   }
$rubies          = [] #['1.9.3-p448', '2.0.0-p247']
$repos           = {
                     'finsync/finsync'                    => 'finsync',
                     'finsync/finsync-yodlee-integration' => 'yodlee'
                   }
$git_prompt      = false #true
$git_user_name   = 'briandavidwetzel'
$git_user_email  = 'briandavidwetzel@gmail.com'
$provision_tasks = [
                     :disable_prompt,
                     :configure_timezone,
                     :install_packages,
                     :configure_zsh,
                     :get_configuration_files,
                     :install_rbenv,
                     :install_rubies,
                     :install_powerline,
                     :configure_postgres,
                     :install_heroku_toolbelt,
                     :clone_repos
                   ]
$home_directory   = '/home/vagrant'
$config_directory = "#{$home_directory}/.config"

#############################################################################################
# InlineProvisioner                                                                         #
#############################################################################################

class InlineProvisioner

  def self.provision
    new.provision $provision_tasks
  end

  def provision(tasks)
    prompt_for_github_credentials if $git_prompt

    "".tap do |p|
      p << comment_banner("Provisioning")
      tasks.each do |t|
        p << send(t)
      end
      p << chown_home_dir
      p << comment_banner("Provisioning Complete")
    end
  end

  private

  #############################################################################################
  # Provisioning Tasks                                                                        #
  #############################################################################################

  def clone_repos
    task 'Clone Repos' do |t|
      $repos.each do |k,v|
        t << clone_into(k, "#{$home_directory}/code/#{v}")
      end
    end
  end

  def disable_prompt
    task 'Disable Prompt' do |t|
      t << 'export DEBIAN_FRONTEND=noninteractive'
    end
  end

  def configure_timezone
    task 'Configure Timezone' do |t|
      t << "locale-gen en_US.UTF-8"
      t << "ln -sf /usr/share/zoneinfo/EST /etc/localtime"
    end
  end

  def install_packages
    task 'Install Packages' do |t|
      t << "add-apt-repository 'deb http://apt.postgresql.org/pub/repos/apt/ saucy-pgdg main'"
      t << "wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -"
      t << "apt-get update"
      t << "apt-get install -q -y tmux vim zsh htop git imagemagick graphviz nodejs postgresql-9.3 postgresql-server-dev-9.3 libssl-dev libxslt-dev libxml2-dev libreadline-dev python-pip exuberant-ctags"
    end
  end

  def configure_zsh
    task 'Configure Zsh' do |t|
      t << clone_into('robbyrussell/oh-my-zsh', "#{$home_directory}/.oh-my-zsh")
      t << "chsh -s $(which zsh) vagrant"
    end
  end

  def install_heroku_toolbelt
    task 'Install Heroku Toolbelt' do |t|
      t << "wget -qO- https://toolbelt.heroku.com/install-ubuntu.sh | sh"
    end
  end

  def install_rbenv
    task 'Install Rbenv' do |t|
      t << clone_into('sstephenson/rbenv', "#{$home_directory}/.rbenv")
      t << clone_into('sstephenson/ruby-build', "#{$home_directory}/.rbenv/plugins/ruby-build")
      t << "chmod a-w #{$config_directory}/sudoers"
      t << "chmod o-r #{$config_directory}/sudoers"
      t << "EDITOR='cp #{$config_directory}/sudoers' visudo"
    end
  end

  def install_rubies
    task 'Install Rubies' do |t|
      $rubies.each do |r|
        t << "echo 'Installing Ruby Version #{r}...'"
        t << as_vagrant_user("rbenv install #{r}")
        t << as_vagrant_user("rbenv rehash")
        t << as_vagrant_user("rbenv shell #{r}")
        t << as_vagrant_user("gem install bundler --no-rdoc --no-ri")
        t << as_vagrant_user("rbenv rehash")
      end
    end
  end

  def get_configuration_files
    task 'Get Configuration Files' do |t|
      t << clone_into('briandavidwetzel/dotconfig', $config_directory)
      t << "sh #{$config_directory}/init.sh #{$home_directory}"
      t << "sh #{$config_directory}/pathogen.sh #{$home_directory}"
    end
  end

  def configure_postgres
    task 'Configure Postgres' do |t|
      t << "su -c 'createuser vagrant -s' postgres"
      t << change_dir('/etc/postgresql/9.3/main/')
      t << "rm pg_hba.conf"
      t << "wget https://raw.github.com/briandavidwetzel/dotconfig/master/pg_hba.conf"
      t << "service postgresql restart"
    end
  end

  def install_powerline
    task 'Install Powerline' do |t|
      t << clone_into('Lokaltog/powerline', "#{$home_directory}/powerline")
      t << "pip install git+git://github.com/Lokaltog/powerline"
    end
  end

  def configure_git
    task 'Configure Git' do |t|
      t << as_vagrant_user("git config --global user.name '#{$git_user_name}'")
      t << as_vagrant_user("git config --global user.email '#{$git_user_email}'")
    end
  end

  def chown_home_dir
    task 'Chown Home Directory' do |t|
      t << "chown -R vagrant:vagrant #{$home_directory}"
    end
  end

  #############################################################################################
  # Helper Methods                                                                            #
  #############################################################################################

  def prompt_for_github_credentials
    puts  "Enter your Github Credentials."
    print "Username: "
    @username = $stdin.gets.chomp
    `stty -echo`
    print "Password (typing will be hidden):"
    @password = $stdin.gets.chomp
    `stty echo`
    puts ""
  end

  def comment_banner(msg)
    banner = ""
    banner << "echo '########################################'\n"
    banner << "echo '# #{msg}...'\n"
    banner << "echo '########################################'\n"
  end

  def task(name, &block)
    "".tap do |s|
      s << comment_banner(name)
      t = []
      yield t
      t.each do |c|
        s << "#{c}\n"
      end
    end
  end

  def clone_into(repo, target)
    task "Clone Into: #{repo}" do |t|
      if $git_prompt
        t << "echo 'using Github credentials'"
        t << "git clone https://#{@username}:#{@password}@github.com/#{repo} #{target}"
      else
        t << "git clone https://github.com/#{repo} #{target}"
      end
    end
  end

  def as_vagrant_user(cmd)
    "sudo -i -H -u vagrant zsh -c \"#{cmd}\""
  end

  def change_dir(dir)
    task "Changing to Directory: #{dir}" do |t|
      t << "cd #{dir}"
    end
  end

  def create_dir(dir)
    task "Creating Directory: #{dir}" do |t|
      t << "mkdir #{dir}"
    end
  end
end

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

    dev.vm.provision :shell, inline: InlineProvisioner.provision
  end

end
