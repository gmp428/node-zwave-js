# FreeTAKServer on Synology DS920+ via Portainer

## What This Deploys

- **FreeTAKServer** — open-source ATAK-compatible CoT server (no license required)
- **FreeTAKServer UI** — web management interface
- All data persisted to `/volume1/docker/freetakserver/data`

---

## Prerequisites

- Synology DS920+ with DSM 7.x
- **Container Manager** (or legacy Docker package) installed from Package Center
- **Portainer** running (typically at `http://192.168.68.50:9000`)

---

## Step 1 — Create the Data Folder

In **File Station**, navigate to `docker/` and create a new folder:

```
/volume1/docker/freetakserver/data
```

---

## Step 2 — Find Your Synology's LAN IP

In DSM go to **Control Panel → Network → Network Interface** and note your LAN IP (e.g. `192.168.1.100`).

---

## Step 3 — Edit the Compose File

Open `docker-compose.yml` and replace both occurrences of `192.168.68.50` with your actual LAN IP:

```yaml
environment:
  - FTS_IP=192.168.1.100   # ← your IP here
```

---

## Step 4 — Deploy via Portainer

1. Open Portainer → **Stacks** → **+ Add stack**
2. Give the stack a name (e.g. `freetakserver`)
3. Select **Web editor** and paste the entire contents of `docker-compose.yml`
4. Click **Deploy the stack**

Portainer will pull the images and start both containers. First pull takes ~1–2 minutes.

---

## Step 5 — Access the Web UI

Once running, open a browser to:

```
http://192.168.68.50:8090
```

> Port 8090 on the host maps to 8080 inside the container (8080 was already occupied on your system).

On first launch you'll be prompted to create an admin account.

---

## Step 6 — Open Ports in Synology Firewall

If you have the Synology firewall enabled (**Control Panel → Security → Firewall**), add rules to allow:

| Port  | Protocol | Purpose                |
|-------|----------|------------------------|
| 8087  | TCP      | ATAK CoT (unencrypted) |
| 8089  | TCP      | ATAK CoT SSL           |
| 19023 | TCP      | REST API               |
| 8090  | TCP      | Web management UI      |

---

## Step 7 — Connect an ATAK Android Client

In the ATAK app:

1. Go to **Settings → Network Preferences → TAK Server**
2. Tap **Add Server**
3. Enter:
   - **Description**: Home TAK Server
   - **IP Address**: `192.168.68.50`
   - **Port**: `8087` (unencrypted) or `8089` (SSL)
   - **Protocol**: TCP
4. Tap **OK** — the status indicator should turn green

---

## Step 8 (Optional) — SSL Certificate Setup

For encrypted connections on port 8089, generate a certificate through the FreeTAKServer UI:

1. Open `http://192.168.68.50:8090`
2. Navigate to **Certificates**
3. Click **Generate** to create a server certificate and client packages
4. Download the `.p12` client package and import it into ATAK:
   - **Settings → Network Preferences → TAK Server → (your server) → Edit → Import Certificate**

---

## Port Reference

| Host Port | Container Port | Service                     |
|-----------|----------------|-----------------------------|
| 8087      | 8087           | CoT unencrypted             |
| 8089      | 8089           | CoT SSL                     |
| 19023     | 19023          | REST API                    |
| 8090      | 8080           | Web UI (remapped from 8080) |

---

## Updating

To update to a newer FreeTAKServer image, in Portainer:

1. Go to **Stacks → freetakserver**
2. Click **Pull and redeploy**

Your data in `/volume1/docker/freetakserver/data` is preserved across updates.
