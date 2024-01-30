![Poarch Creek Indians Federal Services Logo](https://pcifs.com/user/images/assets/SdWFPGMerCAgnyu.svg)
# PCIFS Server Setup

Our servers live on an AWS GovCloud EC2 Instance in the US-Gov-East Reigon.
From there, we create a new instance, t3.micro using the __AMAZON Linux 2023__ AMI.
```
Amazon Linux 2023 AMI 2023.3.20240122.0 x86_64 HVM kernel-6.1
```

1. From the ec2 dashboard, click "Launch Instance"
2. Give the instance a name, ie. "production-web-server"
3. Select Amazon Linux from the "Quick Start" Section of Application and OS Images (AMI) Section
4. Instance Type -  t3.micro
5. Select your generated Key Pair (or go and create one now and return here if you dont see one to select)
6. Everything else can be left default. Select Launch Instance.
7. Attach the correct elastic IP to the new instance.
    - Go to the Elastic IPS tab on the left
    - select the Correct IP to attach  to new server
    - Go to Actions (top of page and Associate Elastic IP)
    - Select Instance
    - Choose your newly created instance in the Instance field.
    - Click the Associate button at the bottom of screen.

After the instance is up and running, the following changes are made to the image mentioned above.

```
sudo dnf update
sudo dnf -y install zsh git util-linux-user httpd mod_ssl php-fpm wget php-mysqli php-json php php-devel php-zip php-gd
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)" "" --unattended
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | bash
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
nvm install node
nvm use node
curl -sS https://webi.sh/gh | sh	
source ~/.config/envman/PATH.env
```

Authenticate with Github (will require 2FA using the website) and clone two of our helper repositories.

```
gh auth login
gh repo clone PCIFS/Configuration ~/config
gh repo clone PCIFS/Scripts ~/scripts
chmod +x ~/scripts/*
```

```
sudo dnf install python3-pip python3-pyOpenSSL
sudo dnf install python3-certbot-apache
pip install certbot
```

Create an SSL cert for localhost and for each virtualhost

```
sudo openssl req -x509 -sha256 -nodes -newkey rsa:2048 -days 365 -keyout /etc/pki/tls/certs/localhost.key -out /etc/pki/tls/certs/localhost.crt
sudo certbot-3 --apache
```

Finish Setting up ZSH

```
~/scripts/setup-zsh
```
