# Trabalho Prático — Pipeline DevSecOps com GitHub Actions

**Disciplina:** DevOps  
**Aluno:** João Marcus Leite da Silva  
**Repositório:** https://github.com/JoaoMarcus12/hello-app

---

## 1. Repositório GitHub

O repositório contém o código-fonte da aplicação e os workflows YAML de CI/CD e segurança:

- **Aplicação:** `app/main.py` — endpoint `GET /hello`
- **Pipeline de build:** `.github/workflows/docker.yml`
- **Pipeline de segurança:** `.github/workflows/security.yml`

**Link:** https://github.com/JoaoMarcus12/hello-app

---

## 2. Evidência de Execução do Pipeline com as Ferramentas Integradas

O pipeline de segurança (`security.yml`) executa automaticamente em Pull Requests para a branch `main`, rodando as quatro ferramentas:

<!-- INSERIR SCREENSHOT: tela do Actions ou do PR mostrando os 4 jobs (Gitleaks, SonarCloud, Trivy, Syft) com status -->

---

## 3. Pull Request Bloqueado — Vulnerabilidade Detectada

Para demonstrar o bloqueio, foi introduzida intencionalmente a dependência `Pillow==8.2.0` (com CVEs conhecidas e correção disponível) na branch `teste-vulnerabilidade`. O Pull Request foi aberto e o Trivy detectou as vulnerabilidades, bloqueando o merge.

### PR bloqueado

<!-- INSERIR SCREENSHOT: tela do PR no GitHub mostrando o check do Trivy com falha (vermelho) -->

### Trecho do relatório do Trivy com as vulnerabilidades

<!-- INSERIR SCREENSHOT: saída do job Trivy no Actions mostrando a tabela de CVEs encontradas -->

---

## 4. Execução Aprovada Após a Correção

Após a remoção da dependência vulnerável e a atualização das dependências transitivas afetadas, o pipeline foi re-executado e todas as verificações passaram.

<!-- INSERIR SCREENSHOT: tela do PR no GitHub mostrando todos os checks com sucesso (verde) -->

---

## 5. SBOM Gerado como Artifact

O Syft gerou o Software Bill of Materials (SBOM) da imagem Docker no formato SPDX-JSON. O arquivo foi publicado como artifact da execução do GitHub Actions.

<!-- INSERIR SCREENSHOT: tela dos Artifacts no GitHub Actions mostrando o arquivo sbom.spdx.json disponível para download -->

O arquivo `sbom.spdx.json` está anexado a esta entrega.

---

## 6. README.md

O README do repositório descreve as ferramentas integradas, as verificações realizadas e as regras de bloqueio:

https://github.com/JoaoMarcus12/hello-app/blob/main/README.md

### Resumo das ferramentas e regras de bloqueio

| Ferramenta | Função | Bloqueio |
|---|---|---|
| **Gitleaks** | Detecção de segredos no histórico do repositório | Bloqueia o PR se encontrar tokens, senhas ou chaves |
| **SonarCloud** | Análise estática (SAST) do código | Bloqueia se o Quality Gate falhar |
| **Trivy** | Varredura de vulnerabilidades na imagem e dependências | Bloqueia para CRITICAL e HIGH com correção disponível; LOW e MEDIUM registrados em relatório |
| **Syft** | Geração de SBOM da imagem Docker | Gera e publica o SBOM como artifact (não bloqueia) |
