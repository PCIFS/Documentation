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
5. Select your generated Key Pair (or go and create one now and return here if you don't see one to select)
6. In Network Settings, click "Create Security Group" and check the following boxes:
    - Allow SSH traffic from Anywhere
    - Allow HTTPS traffic from Anywhere
    - Allow HTTP traffic from Anywhere
8. Everything else can be left default. Select Launch Instance.
9. Attach the correct elastic IP to the new instance.
    - Go to the Elastic IPS tab on the left sidebar
    - Select the Correct IP to attach  to new server
    - Go to Actions (top of page) and select Associate Elastic IP
    - Select Instance
    - Choose your newly created instance in the Instance field.
    - Click the Associate button at the bottom of screen.

After the instance is up and running, login to the server using ssh via your keypair specified above:

```
ssh -i "C:\Path\To\My\Key.pem" ec2-user@INSTANCEPUBLICIP
```

...and run the following command (copy/paste to server)

 ```
sudo dnf update && sudo dnf -y install zsh git util-linux-user httpd mod_ssl php-fpm wget php-mysqli php-json php php-devel php-zip php-gd python3 python3-pip python3-pyOpenSSL augeas-libs && sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)" "" --unattended && curl -sS https://webi.sh/gh | sh	&& source ~/.config/envman/load.sh
```

Authenticate with Github (will require 2FA using the website)... 

```
gh auth login
```
> - Select Github.com
> - Select HTTPS
> - "Authenticate with your github credentials?" Select Yes
> - Select "Login With a web browser"
> - You will be given a code. Copy this Code and press enter.
> - The browser will fail to open, but navigate to (https://github.com/login/device)[https://github.com/login/device], login as PCIFS, and enter the code there.
> - Click "Continue"
> - Click "Authorize github"
> - The command should complete in your terminal window.
  
...and clone two of our helper repositories.
```
gh repo clone PCIFS/Configuration ~/config
gh repo clone PCIFS/Scripts ~/scripts
chmod +x ~/scripts/*
```
pull in website repositories and set up apache configuration, replace SERVER_TYPE in this command with either 'prod','dev', or 'staging' (without quotes) depending on the type of server you are creating. 
```
~/scripts/init-new-web-server SERVER_TYPE
```
Create an SSL cert for localhost and for each virtualhost

```
sudo python3 -m venv /opt/certbot/
sudo /opt/certbot/bin/pip install --upgrade pip
sudo /opt/certbot/bin/pip install certbot certbot-apache
sudo ln -s /opt/certbot/bin/certbot /usr/bin/certbot
sudo certbot --apache
```

> Here, you'll recieve a few prompts:
> 
> For "Enter email address", we generally use:
>  __cieda_web@pcicie.com__
> 
> "Please read the terms of Service..."
> Y
> 
> "Would you be willing...."
> N
> 
> "Which names would you like to activate HTTPS for?...."
> [Press enter]

Enable auto-renewal of the certificates using a cron-job
```
echo "0 0,12 * * * root /opt/certbot/bin/python -c 'import random; import time; time.sleep(random.random() * 3600)' && sudo certbot renew -q" | sudo tee -a /etc/crontab > /dev/null
```

Finish Setting up ZSH

```
~/scripts/setup-zsh
```

Create an admin.yaml user file, using the password stored in dashlane under the name "GRAV MASTER PASS":
```
 /var/www/pcifs.com/grav/bin/plugin login new-user
```

> __Enter a username:__ admin
> 
> __Enter a password:__ [PASSWORD STORED IN DASHLANE UNDER "GRAV MASTER PASS"]
> 
> __Repeat the password:__ [PASSWORD STORED IN DASHLANE UNDER "GRAV MASTER PASS"]
> 
> __Enter an email:__ cieda_web@pcicie.com
> 
> __Enter a language abbreviation:__ en
> 
> __Please Choose a Set of Permissions:__ [a] Admin Access
> 
> __Enter a fullname:__ PCIFS Administrator
> 
> __Enter a title:__ PCIFS Administrator
> 
> __Please choose a state for the account:__ enabled

You should see a message: "Success! User admin created."

Now run the script that will add this new admin user to every other website on the server.
```
~/scripts/create-admin-users
```


