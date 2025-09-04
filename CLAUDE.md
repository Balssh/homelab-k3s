# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This is a GitOps-managed Kubernetes homelab setup using Flux CD for continuous deployment. The repository defines a complete k3s cluster infrastructure with various self-hosted applications and services.

## Architecture

### Core Components
- **Flux CD**: GitOps continuous deployment tool that watches this repository and applies changes to the cluster
- **Traefik**: Ingress controller and reverse proxy for routing external traffic
- **MetalLB**: Load balancer implementation for bare metal Kubernetes
- **cert-manager**: Automatic TLS certificate management via Let's Encrypt
- **Sealed Secrets**: Secure way to store secrets in Git using encryption

### Directory Structure
- `bootstrap/`: Core Flux system configuration and initial cluster setup
  - `flux-system/`: Flux CD system components and Git repository sync config
  - `kustomizations/`: Flux Kustomization resources that define what gets deployed
  - `helmrepositories/`: Helm repository definitions for charts
  - `namespaces/`: Namespace definitions for organizing applications
- `<service>/`: Individual application directories (e.g., `traefik/`, `adguard/`, `glance/`)
  - Each contains HelmRelease manifests and related configuration

### Applications Currently Deployed
- **Traefik**: Reverse proxy with dashboard at `traefik.balssh.com`
- **AdGuard**: DNS filtering and ad blocking
- **Glance**: Dashboard for monitoring services
- **ActualBudget**: Personal finance management
- **Podinfo**: Demo application for testing

## Working with this Repository

### Common Kubernetes Commands
Since this is a Kubernetes-based setup, you'll typically use:
```bash
kubectl get pods -A                    # View all pods across namespaces
kubectl get helmreleases -A            # View Flux HelmReleases
kubectl get kustomizations -A          # View Flux Kustomizations
kubectl logs -n flux-system -l app=flux
flux get sources git                   # Check Flux git repository sync
flux get kustomizations               # Check Flux kustomization status
flux get helmreleases                 # Check Helm release status
```

### Configuration Patterns
- **HelmReleases**: Applications are deployed using Flux HelmRelease CRDs
- **Kustomizations**: Flux watches directories and applies resources via Kustomization CRDs
- **IngressRoutes**: Traefik uses IngressRoute CRDs for routing configuration
- **Values**: Helm chart values are embedded directly in HelmRelease specs

### Adding New Applications
1. Create a new directory for the application (e.g., `newapp/`)
2. Create a HelmRelease manifest: `helmrelease-newapp.yaml`
3. Add namespace definition in `bootstrap/namespaces/`
4. Create Kustomization in `bootstrap/kustomizations/`
5. Add Helm repository if needed in `bootstrap/helmrepositories/`

### Network and Ingress
- External traffic enters through Traefik LoadBalancer (MetalLB provides the external IP)
- Applications expose services via Traefik IngressRoute CRDs
- TLS certificates are automatically managed by cert-manager
- Domain used: `*.balssh.com`

### Secrets Management
- Use sealed-secrets for encrypting secrets that can be safely stored in Git
- Create SealedSecret CRDs rather than plain Kubernetes Secret objects

### Monitoring and Debugging
- Check Flux status: `flux get all`
- View application logs: `kubectl logs -n <namespace> <pod-name>`
- Traefik dashboard provides routing and service status information
- Each HelmRelease includes health checks for deployment verification

## Infrastructure Notes

### Network Setup
The setup includes Wireguard VPN configuration for secure remote access to the homelab, with proper firewall rules and port forwarding from an Oracle VPS to the local cluster.

### Storage
Applications requiring persistence use PVCs, with configurations typically specifying smaller storage sizes (reduced from defaults for resource efficiency).