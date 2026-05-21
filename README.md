# Desafio DevSecOps — Gerenciador de Tarefas

## Sobre o Projeto

Este repositório faz parte do desafio prático do módulo de DevSecOps da ADA Tech.
O projeto foi entregue com vulnerabilidades propositais e uma pipeline incompleta.
A missão foi implementar a esteira de segurança e corrigir todos os problemas encontrados.

## O que foi implementado

- [x] Secrets Scanning com **Gitleaks**
- [x] SAST com **Semgrep**
- [x] SCA com **Grype**
- [x] Deploy com **GitHub Pages**

## Como a pipeline funciona

A pipeline roda automaticamente a cada `push` para a branch `main`.
Ela segue o princípio de **"break the build"**: se qualquer gate de segurança
falhar, o deploy é bloqueado. Nenhum código chega à produção sem passar
pelos checks de segurança.

### Step 1 — Checkout do Código
Faz o download completo do repositório, incluindo todo o histórico de commits.
Isso é essencial para que o Gitleaks consiga escanear não só o código atual,
mas também commits anteriores onde segredos podem ter sido expostos.

### Step 2 — Build
Verifica se os arquivos da aplicação estão presentes e íntegros.
É o pré-requisito para os steps de segurança que vêm a seguir.

### Step 3 — Secrets Scanning com Gitleaks
**O que faz:** Varre todo o código e o histórico de commits em busca de
segredos expostos: senhas, tokens de API, chaves privadas e credenciais.

**Por que é importante:** Um segredo exposto no código pode dar acesso total
a sistemas externos. O Gitleaks detecta esses padrões antes que cheguem à produção.
A pipeline **quebra** se qualquer segredo for encontrado.

**Vulnerabilidade corrigida:** `API_KEY` e `DB_PASSWORD` estavam escritos
diretamente no `script.js`. As linhas foram removidas completamente.

### Step 4 — SAST com Semgrep
**O que faz:** Analisa o código estaticamente em busca de padrões inseguros,
seguindo as regras do OWASP Top 10: XSS, injeções, uso de `eval()`, entre outros.

**Por que é importante:** Encontra falhas de segurança na lógica do código
antes mesmo de rodar a aplicação.
A pipeline **quebra** se vulnerabilidades forem encontradas.

**Vulnerabilidades corrigidas:**
- `innerHTML` com input do usuário substituído por `innerText` (previne XSS)
- `eval()` com input do usuário removido completamente (previne injeção de código)

### Step 5 — SCA com Grype
**O que faz:** Analisa todas as dependências do `package.json` em busca de
CVEs — vulnerabilidades conhecidas em bibliotecas de terceiros.

**Por que é importante:** Uma dependência desatualizada pode ser a porta de
entrada para um ataque, mesmo que o seu próprio código esteja perfeito.
A pipeline **quebra** em vulnerabilidades de severidade média ou maior.

**Vulnerabilidades corrigidas:**

| Dependência | Versão vulnerável | Versão segura | CVE |
|---|---|---|---|
| lodash | 4.17.4 | 4.17.21 | CVE-2019-10744 (Prototype Pollution) |
| express | 4.17.1 | 4.21.2 | Múltiplas CVEs |
| axios | 0.21.1 | 1.7.9 | CVE-2021-3749 (SSRF) |

### Step 6 — Deploy (GitHub Pages)
Só é executado se **todos os steps anteriores passarem**.
Publica a aplicação automaticamente no GitHub Pages.

## Vulnerabilidades encontradas e corrigidas

| # | Arquivo | Vulnerabilidade | Ferramenta | Correção |
|---|---|---|---|---|
| 1 | `script.js` | API_KEY e DB_PASSWORD hardcoded | Gitleaks | Linhas removidas |
| 2 | `script.js` | `innerHTML` com input do usuário (XSS) | Semgrep | Substituído por `innerText` |
| 3 | `script.js` | `eval()` com input do usuário | Semgrep | `eval()` removido |
| 4 | `package.json` | 3 dependências com CVEs conhecidos | Grype | Versões atualizadas |

## URL de Produção

🔗 https://SobreiraNeto84.github.io/projeto-devsecops-desafio
