# Archax Aave protocol interface :ghost:

An open source decentralized liqudity protocol interface from AAVE for Archax setup

Enabling users to:

- Manage and monitor their positions on the Aave Protocol, and the overall status of it
- Manage and monitor their positions on the Aave Safety module
- Participate in the Aave Governance

## How to set up

### Install Node

We are using a Next.js application, which relies on Node. You must have Node installed and set to use the specified version in `.nvmrc`. You can potentially use other versions, but we don’t recommend differing here.

You can download it from the [NodeJS website](https://nodejs.org/en/download/), but we recommend installing nvm and running the following command at root, after cloning or downloading the repo.

```bash
nvm use
```

### Install Dependencies

Download [Yarn Package Manager](https://yarnpkg.com/) and install the dependencies.

```bash
yarn install
```

### Setup Environment Variables

There are a few environment variables that we store for the interface. Before starting development, copy over the environment variables from the provided dummy file to a local copy.

```bash
cp .env.dev .env.local
```

## Get Up & Running

Once you’ve completed the prerequisites above, you should be able to start running the interface locally.

There is an option to build the application for usages as a static site on platforms like IPFS, AWS, Vercel, etc. This essentially builds the application as above and additionally uses the `next export` command. More information around Next’s export command can be found [here](https://nextjs.org/docs/advanced-features/static-html-export).

```bash
yarn build:static
```

You can serve up the static site assets with the following command, which can be viewable on `localhost:3000`.

```bash
yarn serve:static
```

## Set up app running dependency

Since this is demo and prototype running AWS EC2 instance, and we have not dockerise this AAVE application, which take some effort and being un-necessary for now.
To run this instance locally in a background, I use pm2. To install pm2 npm package.

Please run the instruction below, choose ONE of them from below, you dont need to run all of them

### yarn
```bash
yarn global add pm2
```

### npm
```bash
npm install pm2 -g && pm2 update
```

### debian
```bash
apt update && apt install sudo curl && curl -sL https://raw.githubusercontent.com/Unitech/pm2/master/packager/setup.deb.sh | sudo -E bash -
```

## Running the app in background
```bash
pm2 start app-running.config.json
```

## Using your way to expose the service, my demo site is https://aave.godepth.com, although you can only access outside of Archax VPN as it is blocked atm

### Setup reverse-proxy with Nginx or Apache, I used nginx, sample vhost config is below
```
server {
    server_name aave.archax.com;

    location / {
        proxy_pass http://127.0.0.1:3000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    listen 443 ssl; # managed by Certbot
    ssl_certificate /etc/letsencrypt/live/aave.godepth.com/fullchain.pem; # managed by Certbot
    ssl_certificate_key /etc/letsencrypt/live/aave.godepth.com/privkey.pem; # managed by Certbot
    include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot

}

server {
    if ($host = aave.archax.com) {
        return 301 https://$host$request_uri;
    } # managed by Certbot


    listen 80;
    server_name aave.godepth.com;
    return 404; # managed by Certbot
}
```
### Setup the route53 or cloudflare and SSL
