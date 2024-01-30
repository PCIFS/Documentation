![Poarch Creek Indians Federal Services Logo](https://pcifs.com/user/images/assets/SdWFPGMerCAgnyu.svg)
# PCIFS Server Setup

Our servers live on an AWS GovCloud EC2 Instance in the US-Gov-East Reigon.
From there, we create a new instance, t3.micro using the __AMAZON Linux 2023__ AMI.
```
Amazon Linux 2023 AMI 2023.3.20240122.0 x86_64 HVM kernel-6.1
```

After the instance is up and running, the following changes are made to the image mentioned above.
Associate an elastic IP with it.

```
sudo dnf update
sudo dnf -y install zsh git util-linux-user httpd mod_ssl php-fpm wget php-mysqli php-json php php-devel php-zip php-gd
git clone https://github.com/Homebrew/brew ~/.linuxbrew/Homebrew
mkdir ~/.linuxbrew/bin
ln -s ~/.linuxbrew/Homebrew/bin/brew ~/.linuxbrew/bin/brew
eval $(~/.linuxbrew/bin/brew shellenv)
export HOMEBREW_TEMP=/var/tmp
brew install zsh wget gh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)" "" --unattended
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | bash
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
nvm install node
nvm use node
curl -sS https://webi.sh/gh | sh	
source ~/.config/envman/PATH.env
sudo usermod -a -G apache ec2-user
sudo chown -R /var/www ec2-user:apache
```

Authenticate with Github (will require 2FA using the website) and clone two of our helper repositories.

```
gh auth login
gh repo clone PCIFS/Configuration ~/config
gh repo clone PCIFS/Scripts ~/scripts
chmod +x ~/scripts/*
```

Finish Setting up ZSH
```
~/scripts/setup-zsh
```
