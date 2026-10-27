# Hello App

Aplicação HTTP simples com pipeline DevSecOps completo via GitHub Actions.

## Endpoint

```
GET /hello
```

Retorna `"Hello World"` (v1.0) ou `"Hello World 2"` (v2.0).

## Executar localmente

```bash
docker pull ghcr.io/joaomarcus12/hello-app:1.0
docker run -p 8000:8000 ghcr.io/joaomarcus12/hello-app:1.0
curl http://localhost:8000/hello
```

## Workflows

### `docker.yml` — Build e publicação

Dispara em todo **push para `main`** e em **tags `v*.*`**.

| Evento | Tag gerada |
|---|---|
| push para `main` | `latest` |
| tag `v1.0` | `1.0` |
| tag `v2.0` | `2.0` |

A imagem é publicada em: `ghcr.io/joaomarcus12/hello-app`

### `security.yml` — Verificações de segurança

Dispara em todo **Pull Request para `main`**. A publicação da imagem só ocorre após as verificações obrigatórias passarem (proteção de branch configurada).

| Ferramenta | Função | Comportamento de bloqueio |
|---|---|---|
| **Gitleaks** | Detecção de segredos no histórico | Bloqueia o PR se encontrar tokens, senhas ou chaves |
| **SonarCloud** | Análise estática (SAST) | Bloqueia se o Quality Gate falhar para código novo |
| **Trivy** | Varredura de imagem e dependências | Bloqueia para vulnerabilidades CRITICAL e HIGH com correção disponível; LOW e MEDIUM são registradas em relatório sem bloquear |
| **Syft** | Geração de SBOM | Gera e publica o arquivo `sbom.spdx.json` como artifact da execução |

## Secrets necessários

| Secret | Descrição |
|---|---|
| `SONAR_TOKEN` | Token gerado no SonarCloud (Settings → Security) |
| `GITHUB_TOKEN` | Fornecido automaticamente pelo GitHub Actions |

## Proteção de branch

A branch `main` deve ter os seguintes checks obrigatórios configurados em **Settings → Branches → Branch protection rules**:

- `Gitleaks - Secret Detection`
- `SonarCloud - SAST`
- `Trivy - Vulnerability Scan`
