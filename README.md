# yacht

## Server statistics (CPU, memory, disk)

**Quick checks over SSH:**

```bash
htop          # CPU and memory (interactive; q to quit)
free -h       # Memory summary
df -h         # Disk space per filesystem
```

**Web dashboard:** Deploy the **Netdata** template (`templates/netdata.template.yaml`) in Yacht. Open `http://<server>:19999` for a real-time dashboard (CPU, memory, disk, network, Docker containers). Uses host network and read-only host mounts; no config required.

---

## HTTPS for Jellyfin (remote access)

To serve Jellyfin over HTTPS when running it via Yacht on a server, use a reverse proxy. Caddy is used here because it obtains and renews Let's Encrypt certificates automatically.

### Prerequisites

- A domain name with an **A** (or **AAAA**) record pointing to your server’s public IP.
- Ports **80** and **443** open on the server firewall and forwarded on your router to that server.

### 1. Add the Caddy template (on the server)

If you manage templates from this repo: import or copy `templates/caddy-jellyfin.template.yaml` into Yacht (Templates → Add Template). Deploy the **Caddy (reverse proxy for Jellyfin)** app. It uses **host** network so it can bind to 80/443 and reach Jellyfin at `127.0.0.1:8096`.

### 2. Create the Caddyfile on the server

On the server (e.g. over SSH):

```bash
mkdir -p ~/.caddy
```

Create `~/.caddy/Caddyfile` with your real domain (see `templates/caddy-jellyfin.Caddyfile.sample`):

```
jellyfin.yourdomain.com {
	reverse_proxy 127.0.0.1:8096
}
```

Replace `jellyfin.yourdomain.com` with the hostname you use in DNS.

### 3. Configure Jellyfin “Known Proxies”

In Jellyfin: **Dashboard → Networking → Known Proxies**. Add:

```
127.0.0.1
```

This makes Jellyfin trust `X-Forwarded-*` headers from Caddy (which runs on the host and connects to Jellyfin from 127.0.0.1).

### 4. (Optional) Restrict Jellyfin to localhost

To avoid exposing Jellyfin’s port 8096 directly, you can bind it to localhost only. In Yacht, edit the Jellyfin container and set the port mapping to `127.0.0.1:8096:8096` (or equivalent in your template). Then only Caddy (on the same host) can reach it; external access is via HTTPS on 443 only.