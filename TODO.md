# Homelab K3s Modernization TODO

## Phase 1: Infrastructure Foundation 🏗️

### Repository Structure Modernization
- [ ] Create new directory structure (`clusters/production/`, `apps/base/`, `apps/overlays/`)
- [ ] Move applications to organized app structure
- [ ] Consolidate scattered configs (metallb-config, traefik-crds into proper structure)
- [ ] Separate Helm values from HelmRelease manifests
- [ ] Add kustomization.yaml files for proper Flux hierarchy

### GitOps Workflow Improvements
- [ ] Replace manual git pull with proper Flux sync intervals
- [ ] Add pre-commit hooks for YAML validation
- [ ] Set up proper branching strategy (main → staging → production)
- [ ] Configure Flux notifications for deployment status

## Phase 2: Connectivity & Access 🌐

### Cloudflare Tunnel Implementation
- [ ] Create Cloudflare tunnel via dashboard
- [ ] Deploy cloudflared as k8s Deployment via Flux
- [ ] Create SealedSecret with tunnel credentials
- [ ] Configure tunnel to route to Traefik LoadBalancer
- [ ] Update DNS: `*.balssh.com` → Cloudflare proxied
- [ ] Test connectivity with existing services
- [ ] Remove VPS WireGuard dependency

### Security Hardening
- [ ] Update container image versions (AdGuard v0.107.55 → latest)
- [ ] Implement proper RBAC patterns
- [ ] Add network policies for service isolation
- [ ] Configure proper SSL/TLS certificates via cert-manager

## Phase 3: Media Server Stack 🎬

### Core Media Services
- [ ] Deploy Jellyfin/Plex with proper storage configuration
- [ ] Add Sonarr for TV series management
- [ ] Add Radarr for movie management  
- [ ] Deploy qBittorrent with VPN sidecar
- [ ] Configure Jackett/Prowlarr for indexer management

### Storage & Persistence
- [ ] Configure dedicated storage classes for media files
- [ ] Set up proper PVC sizing and storage policies
- [ ] Implement media library organization structure
- [ ] Add backup strategies for configurations

### Media Stack Integration
- [ ] Configure proper ingress routing with Traefik
- [ ] Add authentication integration with Authentik SSO
- [ ] Set up reverse proxy rules for media services
- [ ] Configure download client integration

## Phase 4: Monitoring & Observability 📊

### Metrics & Monitoring
- [ ] Deploy Prometheus + Grafana stack
- [ ] Add node-exporter for system metrics
- [ ] Configure application-specific metrics collection
- [ ] Set up dashboards for media services
- [ ] Add storage usage monitoring

### Alerting & Notifications
- [ ] Configure Prometheus AlertManager
- [ ] Set up Discord/Slack notifications for critical alerts
- [ ] Add uptime monitoring for media services
- [ ] Configure backup job monitoring

## Phase 5: Additional Services & Features ⚡

### Productivity Apps
- [ ] Recipe manager (Tandoor/Mealie)
- [ ] Paperless-ngx for document management
- [ ] Bookmark manager (Linkding/Wallabag)
- [ ] Photo storage (Immich/PhotoPrism)

### Development & Experimentation
- [ ] Add code-server for remote development
- [ ] Set up local Docker registry
- [ ] Add resource quotas and limits for experimentation
- [ ] Configure namespace isolation for different project types

## Phase 6: Automation & Polish ✨

### Backup & Disaster Recovery
- [ ] Implement automated configuration backups
- [ ] Set up media metadata backup strategies
- [ ] Test restoration procedures
- [ ] Document disaster recovery processes

### Performance Optimization
- [ ] Add resource monitoring and alerts
- [ ] Optimize storage performance for media streaming
- [ ] Implement proper caching strategies
- [ ] Add CDN integration where beneficial

### Documentation & Maintenance
- [ ] Update README with current architecture
- [ ] Create troubleshooting guides
- [ ] Document service dependencies and relationships
- [ ] Set up automated dependency updates (Renovate/Dependabot)

---

## Quick Wins (Priority Items) 🚀
1. **Cloudflare Tunnel** - Restore external connectivity immediately
2. **Jellyfin Deployment** - Get media server running quickly
3. **Monitoring Stack** - Visibility into system health
4. **Repository Structure** - Clean foundation for future growth

## Notes
- Keep existing services running during migration
- Test changes in staging branch before main
- Document any issues encountered for future reference
- Maintain backup of v1backup branch throughout process