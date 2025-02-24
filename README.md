# Cluster Configuration

This repository contains the configuration for the Kubernetes cluster.

## Directory Structure

- `kubernetes/` - Kubernetes manifests and configurations
  - `apps/` - Application-specific configurations
  - `core/` - Core cluster configurations
- `talos/` - Talos node configurations (encrypted)
  - `controlplane/` - Control plane node configs
  - `worker/` - Worker node configs

## Encrypted Configurations

Sensitive configurations are encrypted using [age](https://github.com/FiloSottile/age).

### Prerequisites

1. Install age:
```bash
brew install age
```

2. Obtain the age private key (`age.key`) from secure storage.

### Decrypting Configurations

To decrypt a configuration file:
```bash
# Decrypt a single file
age -d -i age.key talos/worker/worker.yaml.age > talos/worker/worker.yaml

# Decrypt all Talos configs
for file in $(find talos -name "*.yaml.age"); do
  age -d -i age.key "$file" > "${file%.age}"
done
```

### Encrypting New Configurations

To encrypt a new configuration file:
```bash
# The public key is: age1gwwpz04tlr6d43cmjgem6rtu7mh06v5v30h4x3dtsjhvdtct79hq98hpfd
age -r age1gwwpz04tlr6d43cmjgem6rtu7mh06v5v30h4x3dtsjhvdtct79hq98hpfd -o config.yaml.age config.yaml
```

## Harbor Registry

Harbor is configured to use TrueNAS NFS storage. To deploy:

1. Copy and configure the templates:
```bash
cp kubernetes/apps/harbor/values.template.yaml kubernetes/apps/harbor/values.yaml
cp kubernetes/apps/harbor/pv.template.yaml kubernetes/apps/harbor/pv.yaml
# Edit the files with your specific values
```

2. Apply the configurations:
```bash
kubectl apply -f kubernetes/core/storage/truenas-nfs.yaml
kubectl apply -f kubernetes/apps/harbor/pv.yaml
helm upgrade --install harbor harbor/harbor -n default -f kubernetes/apps/harbor/values.yaml
```

## Services

Current services planned for migration to Kubernetes:

- Databases
  - MariaDB
  - MongoDB
  - PostgreSQL
  - Redis
- Media
  - Plex
  - PhotoPrism
- Automation
  - Home Assistant
  - Node-RED
- Monitoring
  - Uptime Kuma
  - Cronicle
- Network Services
  - NGinx Proxy Manager
  - AdGuard

## Getting Started

[To be added: Instructions for deploying configurations] 