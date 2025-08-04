# A patched 'CouchDB docker-compose setup for Self-hosted LiveSync with Tailscale funnel' fork

## What is this?

This is an enhanced fork that provides a complete self-hosted CouchDB solution for Obsidian LiveSync with automatic HTTPS through Tailscale Funnel. No SSL certificates or domain management required!

- **Pre-configured CouchDB Server** with optimized CORS settings for Obsidian and Self-hosted LiveSync
- **Automatic HTTPS** via [Tailscale Funnel](https://tailscale.com/kb/1223/funnel) - no SSL certificates needed
- **Zero-config networking** - works out of the box with Docker Compose
- **Production-ready** with proper security settings and persistent data storage

### Major Changes in This Fork

**Enhanced Docker Compose Setup:**

- Separated Tailscale and CouchDB into distinct, properly networked containers
- Added comprehensive capability management (`NET_ADMIN`, `SYS_MODULE`, `NET_RAW`)
- Implemented proper volume mounting for kernel modules and TUN device access
- Added dedicated bridge network for container communication

**Improved Security & Configuration:**

- Environment-based configuration using `.env` files for better security
- Optimized CouchDB settings with increased document and request size limits
- Pre-configured CORS settings for Obsidian compatibility (`app://obsidian.md`, `capacitor://localhost`)
- Proper authentication and admin user setup

**Advanced Tailscale Integration:**

- Dynamic Tailscale Serve configuration with JSON-based routing
- Automatic HTTPS termination with domain-based routing
- Funnel-ready configuration for public internet access
- Container tagging support for better Tailscale ACL management

**Data Persistence:**

- Dedicated volumes for CouchDB data and Tailscale state
- External configuration mounting for easy customization
- Proper backup-friendly data structure

### Prerequisites

- You should have already created an account on tailscale.
- Your tailscale account should have enabled funnel once.

(For more instructions, read the [official document](https://tailscale.com/blog/docker-tailscale-guide)).

### How to use

**Quick Setup:**

1. **Prepare CouchDB configuration:**
   - Copy `.env.couchdb.sample` to `.env.couchdb`
   - Replace `[your-favourite-user-name]` and `[and-your-favourite-password]` with your desired credentials

2. **Configure Tailscale:**
   - Copy `.env.tailscale-app.sample` to `.env.tailscale-app`
   - Replace `[HERE_FOR_YOUR_TAILSCALE_AUTHKEY]` with your Tailscale OAuth key
   - **Note: Following best security practices use a regular ephemeral auth key** (generate it at [Tailscale Admin Console](https://login.tailscale.com/admin/settings/oauth))

3. **Deploy:**

   ```bash
   docker compose up -d
   ```

**First-time Setup:**

On first run, you may need to authorize the Tailscale Funnel. If the setup doesn't complete automatically:

1. Check container logs: `docker compose logs tailscale-app`
2. If needed, manually enable funnel: `docker exec -it tailscale-app tailscale funnel 443`
3. Follow the authorization URL if prompted

**Accessing Your Database:**

- Your CouchDB will be available at: `https://your-tailscale-hostname.ts.net`
- Access the admin interface at: `https://your-tailscale-hostname.ts.net/_utils`
- Use in Obsidian LiveSync with the HTTPS URL (no additional SSL setup required)

### Troubleshooting

If you encounter issues during setup:

1. **Funnel Authorization Error:**

   ```bash
   docker exec -it tailscale-app tailscale funnel 443
   ```

   If you see a message about node access, visit the provided URL to authorize your node:

   ```text
   Funnel is enabled, but the list of allowed nodes in the tailnet policy file does not include the one you are using.
   To give access to this node you can edit the tailnet policy file, or visit:
   https://login.tailscale.com/f/funnel?node=<your-node-id>
   ```

2. **Check Service Status:**

   ```bash
   docker compose ps                    # Check container status
   docker compose logs tailscale-app   # View Tailscale logs
   docker compose logs couchdb-app     # View CouchDB logs
   ```

3. **Verify Network Connectivity:**

   ```bash
   docker exec -it tailscale-app tailscale status  # Check Tailscale connection
   docker exec -it couchdb-app curl -I http://localhost:5984  # Test CouchDB
   ```

### Configuration Files

This fork includes several pre-configured files:

- **`docker-compose.yml`**: Complete container orchestration with proper networking and security
- **`couchdb-etc/myconfig.ini`**: Optimized CouchDB configuration for Obsidian LiveSync  
- **`ts-conf/ts-serve.json`**: Tailscale Serve configuration for HTTPS termination and routing
- **`.env.couchdb.sample`**: Template for CouchDB credentials
- **`.env.tailscale-app.sample`**: Template for Tailscale configuration with OAuth support

### Features

- **Zero SSL Configuration**: No need to manage certificates or DNS
- **Secure by Default**: Pre-configured with proper authentication and CORS
- **Container Isolation**: Separate containers for better security and maintenance
- **Persistent Storage**: Your data survives container restarts and updates
- **Easy Backup**: Simple volume-based data structure for backup strategies
- **Public Access**: Optional internet access through Tailscale Funnel
- **Development Ready**: Easy local development with proper networking

*Made this because it took me multiple tries and lots of time to get up and running with outdated configs over the original one and different cloud providers, enjoy*! ❤️
