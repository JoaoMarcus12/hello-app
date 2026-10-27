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

## Imagens publicadas no GHCR

| Tag | Resposta do endpoint |
|---|---|
| `ghcr.io/joaomarcus12/hello-app:1.0` | `"Hello World"` |
| `ghcr.io/joaomarcus12/hello-app:2.0` | `"Hello World 2"` |
| `ghcr.io/joaomarcus12/hello-app:latest` | `"Hello World 2"` |

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

## SBOM (Software Bill of Materials)

O SBOM da imagem Docker é gerado automaticamente pelo Syft a cada Pull Request e publicado como artifact do GitHub Actions.

Para acessar o SBOM mais recente:
1. Acesse a aba [Actions](https://github.com/JoaoMarcus12/hello-app/actions)
2. Clique na execução mais recente do workflow **Verificações de Segurança**
3. Baixe o artifact **sbom.spdx.json** na seção Artifacts

## Secrets necessários

| Secret | Descrição |
|---|---|
| `SONAR_TOKEN` | Token gerado no SonarCloud (Settings → Security) |
| `GITHUB_TOKEN` | Fornecido automaticamente pelo GitHub Actions |

## Proteção de branch

A branch `main` tem os seguintes checks obrigatórios configurados via ruleset:

- `Gitleaks - Detecção de Segredos`
- `SonarCloud - Análise Estática (SAST)`
- `Trivy - Varredura de Vulnerabilidades`
