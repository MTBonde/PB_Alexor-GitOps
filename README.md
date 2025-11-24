# Alexor GitOps

GitOps repository for Alexor platform - managed by Flux CD.

## Struktur

```
apps/           # Kustomize base + overlays for alle services
clusters/       # Miljø-specifikke Flux konfigurationer
  ├── dev/
  ├── staging/
  └── prod/
```

## Services

- **Infrastructure**: rabbitmq, redis
- **Platform**: auth, registry, relay, session, gameserver

## Brug

Flux synkroniserer automatisk fra `clusters/<miljø>/` og deployer alle apps til det relevante namespace.

## Forstå

Start med at læse:
1. `clusters/dev/alexor-apps.yaml` - Forstå Flux
2. `apps/auth/base/deployment.yaml` - Forstå Kubernetes Deployments
3. `apps/auth/base/service.yaml` - Forstå Services

## Repositories

### Platform Services
| Repository                                                                              | Service    |
|-----------------------------------------------------------------------------------------|------------|
| [PB_SI_HAGI_AuthService](https://github.com/MTBonde/PB_SI_HAGI_AuthService)             | Auth       |
| [PB_SI_HAGI_RegistryService](https://github.com/MTBonde/PB_SI_HAGI_RegistryService)     | Registry   |
| [PB_SI_HAGI_SessionService](https://github.com/MTBonde/PB_SI_HAGI_SessionService)       | Session    |
| [PB_SI_HAGI_RelayService](https://github.com/MTBonde/PB_SI_HAGI_RelayService)           | Relay      |
| [PB_SI_HAGI_GameServerService](https://github.com/MTBonde/PB_SI_HAGI_GameServerService) | GameServer |

### Infrastructure
| Repo                                                                  | Beskrivelse                                      |
|-----------------------------------------------------------------------|--------------------------------------------------|
| [PB_Alexor-GitOps](https://github.com/MTBonde/PB_Alexor-GitOps)       | Dette repo - Kubernetes manifests og Flux config |
| [PB_Alexor-Workflows](https://github.com/MTBonde/PB_Alexor-Workflows) | Centraliserede GitHub Actions workflows          |

### Shared Libraries
| Package                                               | Beskrivelse                                                                       |
|-------------------------------------------------------|-----------------------------------------------------------------------------------|
| [HAGI.Robust](https://github.com/MTBonde/HAGI.Robust) | NuGet package - Polly retry/circuit breaker, health endpoints, dependency waiters |

### Test & Development
| Repo                                                                        | Beskrivelse       |
|-----------------------------------------------------------------------------|-------------------|
| [PB_SI_HAGI_ClientDummy](https://github.com/MTBonde/PB_SI_HAGI_ClientDummy) | Test client       |
| [PB_SI_HAGI_Testing](https://github.com/MTBonde/PB_SI_HAGI_Testing)         | Integration tests |

## Installation

### 1. Installer kubectl

```bash
# Download seneste stabile kubectl binary
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"

# Installer kubectl systemwide med korrekte permissions
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl

# Verificer installation
kubectl version --client
```

### 2. Konfigurer kubeconfig

```bash
# Eksporter microk8s config til fil
sudo microk8s config > kubeconfig-dev.yaml

# Opret .kube directory hvis den ikke findes
mkdir -p ~/.kube

# Flyt config til standard lokation
mv kubeconfig-dev.yaml ~/.kube/config

# Sikre permissions (kun bruger kan læse)
chmod 600 ~/.kube/config

# Verificer connection til cluster
kubectl config current-context
kubectl get ns
```

### 3. Bootstrap Flux CD

```bash
# Bootstrap Flux til GitHub repo
# --owner: GitHub bruger/org
# --repository: GitOps repo navn
# --branch: Branch at synkronisere fra
# --path: Sti til cluster-specifik config
# --personal: Brug personal access token
flux bootstrap github \
  --owner=MTBonde \
  --repository=PB_Alexor-GitOps \
  --branch=main \
  --path=clusters/dev \
  --personal

# Verificer Flux er installeret
kubectl get ns
kubectl get pods -n flux-system
```

### 4. Verificer deployment

```bash
# Oversigt over alle pods i dev namespace
kubectl get pods -n dev

# Oversigt over alle services
kubectl get svc -n dev

# Se alle Flux kustomizations og deres status
flux get kustomizations -A

# Live watch på pods (opdaterer automatisk)
kubectl get pods -n dev -w
```