$ubuntu_url     = "http://cloud-images.ubuntu.com/vagrant/saucy/current/saucy-server-cloudimg-amd64-vagrant-disk1.box"
$hostname       = "development"
$box_name       = "ubuntu-13.10"
$git_user_email = "CHANGE ME!"
$git_user_name  = "CHANGE ME"
$port_forward   = {
  8000 => 8000,
  5000 => 5000
}

$provision  = <<PROVISION
HOME_DIR="/home/vagrant"
echo "##############################"
echo "Provisioning..."
echo "##############################"

# Disable prompt
export DEBIAN_FRONTEND=noninteractive
echo "Configure timezone..."
locale-gen en_US.UTF-8
ln -sf /usr/share/zoneinfo/EST /etc/localtime

echo "##############################"
echo "Installing Packages..."
echo "##############################"
echo "Add postgres apt-repo..."
add-apt-repository "deb http://apt.postgresql.org/pub/repos/apt/ saucy-pgdg main"
wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
apt-get update
apt-get install -q -y tmux vim zsh htop git imagemagick graphviz nodejs \
  postgresql-9.3 postgresql-server-dev-9.3 libssl-dev libxslt-dev libxml2-dev \
  libreadline-dev

echo "##############################"
echo "Configure Zsh..."
echo "##############################"
git clone git://github.com/robbyrussell/oh-my-zsh.git $HOME_DIR/.oh-my-zsh
# will get .zshrc file later...
chsh -s $(which zsh) vagrant

echo "##############################"
echo "Configure Vim..."
echo "##############################"
VIM_BUNDLE_DIR=$HOME_DIR/.vim/bundle
mkdir -p $HOME_DIR/.vim/autoload $HOME_DIR/.vim/bundle
echo "Installing Pathogen..."
curl -Sso $HOME_DIR/.vim/autoload/pathogen.vim https://raw.github.com/tpope/vim-pathogen/master/autoload/pathogen.vim
cd $VIM_BUNDLE_DIR
echo "Installing NERDCommenter..."
git clone git://github.com/scrooloose/nerdcommenter.git
echo "Installing NERDTree..."
git clone git://github.com/scrooloose/nerdtree.git
echo "Installing Buffergator..."
git clone git://github.com/jeetsukumaran/vim-buffergator.git
echo "Installing Bundler..."
git clone git://github.com/tpope/vim-bundler.git
echo "Installing CoffeeScript..."
git clone https://github.com/kchmck/vim-coffee-script.git
echo "Installing Fugitive..."
git clone git://github.com/tpope/vim-fugitive.git
echo "Installing Handlebars..."
git clone git://github.com/nono/vim-handlebars.git
echo "Installing Rails..."
git clone git://github.com/tpope/vim-rails.git
echo "Installing Rake..."
git clone git://github.com/tpope/vim-rake.git
echo "Installing Sensible..."
git clone git://github.com/tpope/vim-sensible.git

echo "##############################"
echo "Download Dotfiles..."
echo "##############################"
cd $HOME_DIR
curl https://raw.github.com/briandavidwetzel/env/master/get | sh

echo "##############################"
echo "Install Powerline..."
echo "##############################"
cd $HOME_DIR
git clone https://github.com/Lokaltog/powerline.git

echo "##############################"
echo "Installing Rbenv..."
echo "##############################"
git clone https://github.com/sstephenson/rbenv.git $HOME_DIR/.rbenv
git clone https://github.com/sstephenson/ruby-build.git $HOME_DIR/.rbenv/plugins/ruby-build
echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> $HOME_DIR/.zshrc
echo 'eval "$(rbenv init -)"' >> $HOME_DIR/.zshrc

echo "##############################"
echo "Installing Ruby 2.0.0-p353..."
echo "##############################"
#TODO: Install ruby versions
# su  -c "CONFIGURE_OPTS='--with-readline' rbenv install 2.0.0-p247" -s /bin/sh vagrant

echo "##############################"
echo "Configure Postgresql..."
echo "##############################"
su -c 'createuser vagrant -s' postgres
cd /etc/postgresql/9.3/main/
rm pg_hba.conf
wget https://raw.github.com/briandavidwetzel/env/master/pg_hba.conf
service postgresql restart

echo "##############################"
echo "Configure Postgresql..."
echo "##############################"
wget -qO- https://toolbelt.heroku.com/install-ubuntu.sh | sh

cd $HOME_DIR

mkdir $HOME_DIR/code
git config --global user.email "#{$git_user_email}"
git config --global user.name "#{$git_user_name}"

# Do last
chown -R vagrant:vagrant $HOME_DIR
PROVISION

Vagrant.configure("2") do |config|

  config.vm.define :prod do |dev|
    dev.vm.box = $box_name
    dev.vm.hostname = $hostname
    dev.vm.box_url = $ubuntu_url

    $port_forward.each do |k,v|
      dev.vm.network :forwarded_port, :guest => k, :host => v
    end

    config.vm.provider "virtualbox" do |v|
        v.memory = 2056
        v.customize ["modifyvm", :id, "--ioapic", "on"]
        v.customize ["modifyvm", :id, "--cpus", 2]
    end

    dev.vm.provision :shell, inline: $provision
  end

end
