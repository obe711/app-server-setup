# App Server Setup

A tutorial on setting up an app server on a cloud server.

## Table of Contents

- [Register Domain](#register-domain)
- [Cloudflare](#cloudflare)
- [Nameservers](#nameservers)
- [Digital Ocean API](#digital-ocean-api)
- [Create Server](#create-server)
- [SSH](#ssh)
- [Install Dependencies](#install-dependencies)
- [Create Site](#create-site)
- [DNS](#dns)
- [SSL](#ssl)
- [Nginx](#nginx)

## Register Domain

For this tutorial we will use namecheap.com. Register a cheap domain name.

## Cloudflare

Click the “Add Site” button. After cloudflare creates the site on your account it will ask you to point your domain’s namesevers to theirs. (ex: “alex.ns.cloudflare.com” and “jessica.ns.cloudflare.com”)

## Nameservers

Navigate to the nameservers settings of your domain. On namecheap.com, click the Manage button for your domain. Then in the nameservers section, select “Custom DNS” then enter “alex.ns.cloudflare.com” and “jessica.ns.cloudflare.com”. Make sure you click the save button (green check).

## Digital Ocean API

Log in to digitalocean.com. Click the “API” menu option on the sidebar. Click the “Generate New Token” button. Create a new token with a unique name and Read & Write scopes. Copy the shown token and save it somewhere. It will not be shown again.

Sign in to forge. Click the “Create Server” button. Click the “Manage Providers” button. Select Digital Ocean, enter a profile name and the API key we just created then click the “Add” button.

## Create Server

Sign in to forge.laravel.com. Click the “Create Server” button and select Digital Ocean. For this tutorial we will use “App Server” as the type. Fill out the rest of the form. For the database, I personally like MariaDB as a sql server, but any option will be fine. Press the “Create Server” button. Copy the Sudo and Database passwords and save them. They will not be shown again.

## SSH

**Check for keys**

```bash
cd ~/.ssh && ls
```

If you see the files, `id_rsa` and `id_rsa.pub` then you can skip the Create Keys step.

**Create keys**

```bash
cd ~/.ssh && ssh-keygen
```

Hit enter until you return to a command prompt.

**Add ssh key to server**
After your server has been provisioned, click the SSH Keys menu option on the side menu. Enter a name for your key. Then:

```bash
cd ~/.ssh && cat id_rsa.pub
```

Copy the output and paste it in the “Public Key” form textarea. Click the “Add” button.

**SSH into server**

```bash
ssh forge@<server ip address>
```

Update

```bash
sudo apt update && sudo apt upgrade -y
sudo reboot
```

## Install Dependencies

**MongoDB**\
[Install Mongo](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-ubuntu/)

```bash
curl https://raw.githubusercontent.com/obe711/app-server-setup/main/scripts/mongodb.sh | bash
```

**NVM + NODE LTS + YARN**\
[Install NVM](https://github.com/nvm-sh/nvm#install--update-script)
[Node JS LTS](https://nodejs.org/en/)

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | bash
exit
```

Then SSH back into server

```bash
ssh forge@<server ip address>
nvm install 16.13.2
npm install -g npm@8.3.1
npm install -g yarn
```

## Create Site

Click the “Sites” menu option on the side menu. In the “New Site” form, enter your domain name and select project type “General PHP / Laravel”. Click “Add” button.

## DNS

On cloudflare.com, select your site then click the “DNS” option on the side menu. Change the A record for your domain to the IP address of your server.

## SSL

Click the “SSL/TLS” option on the side menu then click the “Origin Server” option. Click the “Create Certificate” button. Select “RSA (2048)” key type then click “Create’. Copy the cert and key.

In forge, click on your site in the “Active Sites” table. Click the “SSL” option on the side menu. Click the “Install Existing” button and then copy and paste your cert and key. Save and then activate.

## Nginx

To proxy to your app server, use the following config:

```
location / {
        proxy_pass http://127.0.0.1:3005;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
```

## 🔗 Links

[Cloudflare](https://dash.cloudflare.com/login)\
[Digital Ocean](https://www.digitalocean.com)\
[Forge Laravel](https://forge.laravel.com)\
[Namecheap](https://www.namecheap.com)
