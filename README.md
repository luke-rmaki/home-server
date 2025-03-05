# Home Server

- [x] Syncthing
- [x] Calibre
- [x] Deluge (for legitimate reasons only)
- [x] File Browser

## Set up

1. Clone this repo
2. Run

```bash
mkdir .fb
touch .fb/filebrowser.db
```

# Instructions

## Install cloudflared

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

## Set up tunnel

```bash
cloudflared tunnel create my-tunnel
```

Create the config file:

```yml
url: http://localhost:8000cloudflared tunnel --config /path/your-config-file.yml run <UUID or NAME>
tunnel: <Tunnel-UUID>
credentials-file: /home/luke/.cloudflared/<Tunnel-UUID>.json
```

```bash
cloudflared tunnel route dns <UUID or NAME> <hostname>
cloudflared tunnel --config /path/your-config-file.yml run <UUID or NAME>
```
