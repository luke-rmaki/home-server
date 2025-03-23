# Home Server

## Set up

### Install Docker

Set up APT repository

```bash
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/raspbian/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/raspbian \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```

Install Docker:
`sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin`

---

## Set up project

- Clone this repo
- Set up .env file.
  - Set D_DIR to be a subpath of F_DIR to have downloaded files served by File Server
- Run

```bash
mkdir .fb
touch .fb/filebrowser.db

```

---

### Install cloudflared

```bash
# If brew installed
brew install cloudflared

# Using APT
sudo mkdir -p --mode=0755 /usr/share/keyrings
curl -fsSL https://pkg.cloudflare.com/cloudflare-main.gpg | sudo tee /usr/share/keyrings/cloudflare-main.gpg >/dev/null
echo "deb [signed-by=/usr/share/keyrings/cloudflare-main.gpg] https://pkg.cloudflare.com/cloudflared any main" | sudo tee /etc/apt/sources.list.d/cloudflared.list
sudo apt-get update && sudo apt-get install cloudflared

# login
cloudflared tunnel login
```

#### Set up tunnel

```bash
cloudflared tunnel create home-server
```

You'll need to delete any with the same name created previously

Change this value in the [tunnel config file](tunnel.yml) to be the id of the tunnel created:

```yml
tunnel: tunnel-id
```

Change these lines in the [docker compose file](docker-compose.yml) to be wherever the files were created on the local machine

```yml
volumes:
  - /home/luke/.cloudflared/tunnel-id.json:/etc/cloudflared/cred.json
  - /home/luke/.cloudflared/cert.pem:/etc/cloudflared/cert.pem
```

Run

```bash
chmod 644 /home/luke/.cloudflared/tunnel-id.json

cloudflared tunnel route dns home-server syncthing.rmaki.tech
cloudflared tunnel route dns home-server calibre.rmaki.tech
cloudflared tunnel route dns home-server lib.rmaki.tech
cloudflared tunnel route dns home-server files.rmaki.tech
cloudflared tunnel route dns home-server deluge.rmaki.tech

docker compose up -d
```
