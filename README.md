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
| [unrealmp_server](https://github.com/alexanderlind98/unrealmp_server) | GameServer |

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

## Brug

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