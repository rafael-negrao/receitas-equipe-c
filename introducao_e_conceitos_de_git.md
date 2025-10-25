# Introdução e Conceitos Fundamentais do Git

## Objetivo

Compreender o que é o Git, por que ele foi criado, como ele funciona internamente e as principais terminologias que sustentam o controle de versão distribuído.  
Este módulo constrói a base teórica necessária antes de realizar qualquer comando prático.

---

## Índice

1. [O Problema que o Git Resolve](#1-o-problema-que-o-git-resolve)
2. [O Que é Git](#2-o-que-é-git)
3. [Git vs GitHub](#3-git-vs-github)
4. [Como o Git Enxerga o Mundo](#4-como-o-git-enxerga-o-mundo)
5. [Estrutura Interna do Git](#5-estrutura-interna-do-git)
6. [Referências e Ponteiros: HEAD, Branches e Tags](#6-referências-e-ponteiros-head-branches-e-tags)
7. [As Três Áreas do Git](#7-as-três-áreas-do-git)
8. [Repositório Local vs Remoto](#8-repositório-local-vs-remoto)
9. [Commits e Mensagens Claras](#9-commits-e-mensagens-claras)
10. [Versionamento Semântico](#91-versionamento-semântico)
11. [Configuração do Git](#10-configuração-do-git)
12. [Integridade e Segurança](#11-integridade-e-segurança)
13. [Arquivos .gitignore e .gitattributes](#12-arquivos-gitignore-e-gitattributes)
14. [Princípio da História Imutável](#13-princípio-da-história-imutável)
15. [Vocabulário Essencial](#14-vocabulário-essencial)
16. [Resumo Visual](#15-resumo-visual)
17. [Comandos Úteis para Diagnóstico](#16-comandos-úteis-para-diagnóstico)
18. [Glossário Bilíngue](#17-glossário-bilíngue)
19. [Perguntas Frequentes](#18-perguntas-frequentes)
20. [Dicas de Resolução de Problemas](#19-dicas-de-resolução-de-problemas)
21. [Referências e Recursos Adicionais](#20-referências-e-recursos-adicionais)

---

## 1. O Problema que o Git Resolve

Antes do Git, os desenvolvedores enfrentavam problemas como:

- Perda de versões de arquivos.
- Dificuldade em trabalhar simultaneamente com outras pessoas.
- Falta de histórico claro sobre *quem alterou o quê e quando*.

Esses problemas levaram à criação de **sistemas de controle de versão (VCS – Version Control Systems)**.

### Tipos de VCS

| Tipo         | Descrição                                                                                          | Exemplos                                           |
|--------------|----------------------------------------------------------------------------------------------------|----------------------------------------------------|
| Local        | Versões armazenadas na máquina do usuário.                                                         | Backup manual, renomear arquivos ("final_v2.zip"). |
| Centralizado | Um servidor central armazena o histórico e os desenvolvedores fazem *check-in/check-out*.          | CVS, Subversion (SVN).                             |
| Distribuído  | Cada desenvolvedor possui o histórico completo localmente; a colaboração ocorre via sincronização. | Git, Mercurial.                                    |

---

## 2. O Que é Git

O **Git** é um sistema **distribuído de controle de versão (DVCS)** criado por **Linus Torvalds** em 2005 para o desenvolvimento do kernel do Linux.  
Ele foi projetado com foco em:

- **Velocidade**
- **Integridade dos dados**
- **Trabalho não linear (branches e merges eficientes)**
- **Distribuição completa** (cada clone tem o histórico completo)

---

## 3. Git vs GitHub

- **Git** é a ferramenta local de versionamento.  
  → Armazena e gerencia versões de arquivos no seu computador.
- **GitHub** é uma plataforma de hospedagem para repositórios Git.  
  → Facilita colaboração, revisão de código (*Pull Requests*), Issues, e CI/CD.

> 💡 **Outros serviços similares:** GitLab, Bitbucket, Azure DevOps Repos.

---

## 4. Como o Git Enxerga o Mundo

O Git **não** trabalha com *arquivos como sequência de linhas*, mas com **snapshots** de todo o projeto a cada commit.

Cada **commit** representa:

- Um *snapshot* completo dos arquivos rastreados naquele momento.
- Um *link* para o commit anterior (histórico encadeado).
- Uma *mensagem descritiva* sobre a mudança.

> Em vez de armazenar "diferenças" (deltas), o Git armazena "fotos completas", reutilizando conteúdo idêntico via deduplicação.

---

## 5. Estrutura Interna do Git

O Git armazena seus dados dentro da pasta oculta `.git/`.  
Lá ficam os **objetos fundamentais**:

| Tipo de Objeto | Descrição                                                           |
|----------------|---------------------------------------------------------------------|
| **Blob**       | Representa o conteúdo bruto de um arquivo.                          |
| **Tree**       | Representa um diretório (lista blobs + subdiretórios).              |
| **Commit**     | Contém referência à tree principal, autor, data, mensagem e *pais*. |
| **Tag**        | Rótulo que aponta para um commit (pode ser leve ou anotada).        |

Cada objeto é identificado por um **hash criptográfico (SHA-1)**, garantindo integridade e autenticidade.

### Estrutura do diretório .git/

```
.git/
├── HEAD
├── config
├── objects/
│   ├── [hash]/
│   └── ...
├── refs/
│   ├── heads/
│   └── tags/
└── index
```

---

## 6. Referências e Ponteiros: HEAD, Branches e Tags

### 🔹 HEAD

É um **ponteiro simbólico** que indica o *commit atual*.  
Normalmente, o HEAD aponta para uma *branch*, e esta aponta para um *commit*.

- Exemplo:  
  `HEAD → refs/heads/main → <hash_do_commit>`

Quando o HEAD aponta diretamente para um commit, chamamos de **detached HEAD** (modo de leitura ou testes).

### 🔹 Branch (Ramificação)

Uma *branch* é um **ponteiro móvel** para um commit.  
Cada novo commit avança o ponteiro dessa branch.

#### 🎯 Por Que Branches São Fundamentais?

Branches permitem **desenvolvimento paralelo** sem afetar o código principal. Imagine trabalhar em uma nova funcionalidade enquanto outra pessoa corrige um bug crítico — ambos
podem trabalhar simultaneamente sem conflitos.

#### 📐 Anatomia de uma Branch

```
A---B---C  (main)
              \
               D---E  (feature/nova-funcionalidade)
```

- **main**: Branch principal, representa código estável
- **feature/nova-funcionalidade**: Branch de desenvolvimento isolado
- Commits A, B, C existem em ambas as branches
- Commits D, E existem apenas na branch de feature

#### 🔄 Ciclo de Vida de uma Branch

1. **Criação**: Nova linha de desenvolvimento

```shell script
git branch feature/login
   git checkout feature/login
   # ou em um comando:
   git checkout -b feature/login
```

2. **Desenvolvimento**: Commits são adicionados

```shell script
git add .
   git commit -m "feat: adiciona formulário de login"
```

3. **Integração**: Merge de volta à branch principal

```shell script
git checkout main
   git merge feature/login
```

4. **Limpeza**: Remoção da branch após merge

```shell script
git branch -d feature/login
```

#### 🌳 Tipos Comuns de Branches

| Tipo                | Nomenclatura        | Propósito                    | Exemplo                          |
|---------------------|---------------------|------------------------------|----------------------------------|
| **Principal**       | `main`, `master`    | Código em produção           | `main`                           |
| **Desenvolvimento** | `develop`, `dev`    | Integração contínua          | `develop`                        |
| **Feature**         | `feature/*`         | Nova funcionalidade          | `feature/carrinho-compras`       |
| **Bugfix**          | `bugfix/*`, `fix/*` | Correção de bugs             | `bugfix/corrige-login`           |
| **Hotfix**          | `hotfix/*`          | Correção urgente em produção | `hotfix/vulnerabilidade-critica` |
| **Release**         | `release/*`         | Preparação de versão         | `release/v2.0.0`                 |
| **Experimental**    | `experiment/*`      | Testes e POCs                | `experiment/nova-arquitetura`    |

#### 🔀 Estratégias de Merge

**Fast-Forward Merge** (quando não há commits divergentes):

```
Antes:
    A---B---C  (main)
         \
          D---E  (feature)

Depois:
    A---B---C---D---E  (main, feature)
```

**Three-Way Merge** (quando há commits em ambas as branches):

```
Antes:
    A---B---C---F  (main)
         \
          D---E  (feature)

Depois:
    A---B---C---F---M  (main)
         \         /
          D---E---/  (feature)
          
    M = commit de merge
```

#### 🎨 Modelos de Branching

**Git Flow** (complexo, para projetos grandes):

- `main`: Produção
- `develop`: Desenvolvimento
- `feature/*`: Funcionalidades
- `release/*`: Preparação de lançamento
- `hotfix/*`: Correções urgentes

**GitHub Flow** (simples, para entrega contínua):

- `main`: Sempre deployável
- `feature/*`: Qualquer mudança (feature, fix, etc.)
- Pull Requests para revisão antes do merge

**GitLab Flow** (híbrido):

- `main`: Desenvolvimento
- `production`: Código em produção
- `feature/*`: Mudanças isoladas

#### 🚨 Boas Práticas com Branches

✅ **Faça:**

- Use nomes descritivos: `feature/adiciona-autenticacao-oauth`
- Mantenha branches pequenas e focadas
- Faça merge/delete após conclusão
- Sincronize frequentemente com a branch principal
- Use Pull Requests para revisão de código

❌ **Evite:**

- Branches de longa duração (aumentam conflitos)
- Nomes genéricos: `teste`, `mudancas`, `fix`
- Acumular muitas branches sem uso
- Fazer merge sem testar
- Trabalhar direto na `main`

#### 🔍 Comandos Essenciais de Branch

| Comando                   | Descrição                                  |
|---------------------------|--------------------------------------------|
| `git branch`              | Lista branches locais                      |
| `git branch -a`           | Lista todas as branches (locais e remotas) |
| `git branch nome`         | Cria nova branch                           |
| `git checkout nome`       | Troca para branch                          |
| `git checkout -b nome`    | Cria e troca para branch                   |
| `git branch -d nome`      | Deleta branch (seguro)                     |
| `git branch -D nome`      | Força deleção de branch                    |
| `git merge nome`          | Mescla branch no HEAD atual                |
| `git branch -m novo-nome` | Renomeia branch atual                      |
| `git push origin nome`    | Envia branch para remoto                   |

#### 📊 Visualizando Branches

```shell script
# Histórico com gráfico de branches
git log --oneline --graph --all --decorate

# Ver branches e seus últimos commits
git branch -v

# Ver quais branches foram mergeadas
git branch --merged

# Ver branches não mergeadas
git branch --no-merged
```

#### 🎭 Exemplo Prático Completo

```shell script
# 1. Criar nova feature
git checkout -b feature/adiciona-pesquisa

# 2. Desenvolver
echo "função de pesquisa" > pesquisa.js
git add pesquisa.js
git commit -m "feat: implementa busca básica"

# 3. Mais trabalho
echo "testes de pesquisa" > pesquisa.test.js
git add pesquisa.test.js
git commit -m "test: adiciona testes de busca"

# 4. Voltar para main e atualizar
git checkout main
git pull origin main

# 5. Mergear feature
git merge feature/adiciona-pesquisa

# 6. Enviar para remoto
git push origin main

# 7. Limpar branch local
git branch -d feature/adiciona-pesquisa

# 8. Limpar branch remota (se existir)
git push origin --delete feature/adiciona-pesquisa
```

### 🔹 Tag

Uma *tag* é um **ponteiro fixo**, usado para marcar versões específicas (ex.: `v1.0.0`).

#### Tipos de Tags

**Tag Leve** (apenas um ponteiro):

```shell script
git tag v1.0.0
```

**Tag Anotada** (recomendada - contém metadados):

```shell script
git tag -a v1.0.0 -m "Versão 1.0.0 - Lançamento inicial"
```

#### Comandos de Tags

| Comando                           | Descrição                |
|-----------------------------------|--------------------------|
| `git tag`                         | Lista todas as tags      |
| `git tag -a v1.0 -m "msg"`        | Cria tag anotada         |
| `git tag v1.0`                    | Cria tag leve            |
| `git push origin v1.0`            | Envia tag para remoto    |
| `git push origin --tags`          | Envia todas as tags      |
| `git tag -d v1.0`                 | Remove tag local         |
| `git push origin :refs/tags/v1.0` | Remove tag remota        |
| `git show v1.0`                   | Exibe informações da tag |

> 💡 **Nota**: Tags são usadas para marcar releases e seguem o padrão de Versionamento Semântico (ver seção 9.1).

---

## 7. As Três Áreas do Git

O Git trabalha com três "camadas" principais:

| Área                      | Nome Técnico     | O que Contém                                               | Comando-Chave            |
|---------------------------|------------------|------------------------------------------------------------|--------------------------|
| 🗂️ **Working Directory** | Working Tree     | Seus arquivos atuais no disco (edições livres).            | `git status`, `git diff` |
| 🧾 **Staging Area**       | Index            | Área de preparação: o que será incluído no próximo commit. | `git add`                |
| 📦 **Repository**         | Local Repository | Histórico consolidado dentro de `.git/`.                   | `git commit`             |

Fluxo típico:

```
Working Directory → git add → Staging Area → git commit → Repository
```

> 💡 **DICA**: Use `git status` frequentemente para entender o estado do repositório.

---

## 8. Repositório Local vs Remoto

### Local Repository

Armazenado no seu computador.  
Permite criar commits mesmo **offline**.

### Remote Repository

Versão hospedada (ex.: GitHub).  
Usada para **sincronizar** trabalho com outros desenvolvedores.

### Principais comandos:

| Ação               | Comando                       | Descrição                                              |
|--------------------|-------------------------------|--------------------------------------------------------|
| Conectar remoto    | `git remote add origin <url>` | Liga o repo local a um remoto.                         |
| Enviar mudanças    | `git push`                    | Envia commits locais ao remoto.                        |
| Trazer mudanças    | `git pull`                    | Atualiza o repo local com commits remotos.             |
| Baixar sem aplicar | `git fetch`                   | Baixa dados do remoto sem alterar seu histórico local. |

---

## 9. Commits e Mensagens Claras

Cada commit representa uma *unidade lógica de trabalho*.

### Boas práticas

- Faça commits **pequenos e coesos**.
- Mensagens devem explicar **o porquê da mudança**, não apenas o quê.

> ✅ **BOA PRÁTICA**: Commits pequenos e frequentes são melhores que commits grandes.

- Use **verbos no imperativo**:
    - ✅ "Adiciona validação de e-mail"
    - ❌ "Adicionando validação de e-mail"

### Padrão *Conventional Commits* (opcional)

```shell script
feat: adiciona API de login
fix: corrige bug de autenticação
docs: atualiza README
refactor: melhora legibilidade do código
```

### 🎯 Exercício Prático

1. Crie um novo repositório Git local
2. Configure seu nome e email
3. Crie um arquivo README.md
4. Adicione-o à staging area
5. Faça seu primeiro commit

---

## 9.1. Versionamento Semântico

O **Versionamento Semântico (SemVer)** é um padrão para numerar versões de software de forma consistente e previsível.

### 📏 Estrutura da Versão

```
MAJOR.MINOR.PATCH[-PRERELEASE][+BUILD]

Exemplo: 2.4.1-beta.1+20231015
```

#### Componentes

| Componente     | Quando Incrementar                                               | Exemplo                       |
|----------------|------------------------------------------------------------------|-------------------------------|
| **MAJOR**      | Mudanças incompatíveis com versões anteriores (breaking changes) | `1.0.0` → `2.0.0`             |
| **MINOR**      | Novas funcionalidades compatíveis com versão anterior            | `1.0.0` → `1.1.0`             |
| **PATCH**      | Correções de bugs compatíveis                                    | `1.0.0` → `1.0.1`             |
| **PRERELEASE** | Versão pré-lançamento (opcional)                                 | `1.0.0-alpha`, `1.0.0-beta.2` |
| **BUILD**      | Metadados de build (opcional)                                    | `1.0.0+20231015`              |

### 🎯 Regras Fundamentais

1. **Versão inicial de desenvolvimento**: `0.y.z`
    - API pode mudar a qualquer momento
    - Não deve ser usada em produção

2. **Primeira versão estável**: `1.0.0`
    - Define API pública
    - A partir daqui, versões seguem as regras

3. **PATCH**: `1.0.0` → `1.0.1`
    - Apenas correções de bugs
    - Não adiciona funcionalidades
    - Não quebra compatibilidade

```shell script
git tag -a v1.0.1 -m "fix: corrige validação de CPF"
```

4. **MINOR**: `1.0.0` → `1.1.0`
    - Adiciona funcionalidades
    - Mantém compatibilidade
    - Pode depreciar funcionalidades antigas
    - PATCH volta a 0

```shell script
git tag -a v1.1.0 -m "feat: adiciona suporte a OAuth2"
```

5. **MAJOR**: `1.0.0` → `2.0.0`
    - Mudanças incompatíveis
    - Remove funcionalidades deprecadas
    - Altera comportamento de APIs existentes
    - MINOR e PATCH voltam a 0

```shell script
git tag -a v2.0.0 -m "BREAKING CHANGE: remove suporte a Python 2"
```

### 🏷️ Versões Pré-lançamento

Indicam que a versão é instável e pode não satisfazer requisitos de compatibilidade.

**Formato**: `MAJOR.MINOR.PATCH-IDENTIFICADOR[.NUMERO]`

**Exemplos**:

```
1.0.0-alpha      # Primeira versão alfa
1.0.0-alpha.1    # Alfa 1
1.0.0-alpha.2    # Alfa 2
1.0.0-beta       # Primeira versão beta
1.0.0-beta.1     # Beta 1
1.0.0-rc.1       # Release Candidate 1
1.0.0            # Versão final
```

**Ordem de precedência**:

```
1.0.0-alpha < 1.0.0-alpha.1 < 1.0.0-beta < 1.0.0-rc.1 < 1.0.0
```

### 🔧 Metadados de Build

Não influenciam precedência de versão, apenas informações adicionais.

**Exemplos**:

```
1.0.0+20231015
1.0.0+exp.sha.5114f85
1.0.0-beta+exp.sha.5114f85
```

### 📋 Fluxo Prático de Versionamento

#### Cenário 1: Correção de Bug

```shell script
# Versão atual: 1.2.3
# Corrigiu bug crítico

git checkout main
git pull origin main

# Fazer correção...
git add .
git commit -m "fix: corrige vazamento de memória no módulo de cache"

# Incrementar PATCH
git tag -a v1.2.4 -m "fix: corrige vazamento de memória"
git push origin main --tags
```

#### Cenário 2: Nova Funcionalidade

```shell script
# Versão atual: 1.2.4
# Adicionou nova feature

git checkout -b feature/notificacoes-push
# Desenvolver feature...
git commit -m "feat: adiciona sistema de notificações push"

git checkout main
git merge feature/notificacoes-push

# Incrementar MINOR
git tag -a v1.3.0 -m "feat: adiciona sistema de notificações push"
git push origin main --tags
```

#### Cenário 3: Breaking Change

```shell script
# Versão atual: 1.3.0
# Mudança incompatível na API

git checkout -b refactor/nova-api-v2
# Refatorar API...
git commit -m "refactor!: redesenha API de autenticação

BREAKING CHANGE: O método authenticate() agora retorna Promise em vez de callback"

git checkout main
git merge refactor/nova-api-v2

# Incrementar MAJOR
git tag -a v2.0.0 -m "BREAKING CHANGE: nova API de autenticação"
git push origin main --tags
```

#### Cenário 4: Pré-lançamento

```shell script
# Versão atual: 2.0.0
# Preparando próxima versão major

# Alpha
git tag -a v3.0.0-alpha -m "chore: versão alfa da v3.0.0"
git push origin --tags

# Beta (após mais desenvolvimento)
git tag -a v3.0.0-beta.1 -m "chore: primeira versão beta da v3.0.0"
git push origin --tags

# Release Candidate
git tag -a v3.0.0-rc.1 -m "chore: release candidate 1 da v3.0.0"
git push origin --tags

# Release final
git tag -a v3.0.0 -m "chore: lançamento da versão 3.0.0"
git push origin --tags
```

### 🎨 Integrando SemVer com Conventional Commits

Conventional Commits podem determinar automaticamente o tipo de versão:

| Tipo de Commit             | Impacto no SemVer    | Exemplo                                        |
|----------------------------|----------------------|------------------------------------------------|
| `fix:`                     | PATCH                | `fix: corrige validação` → `1.0.0` → `1.0.1`   |
| `feat:`                    | MINOR                | `feat: adiciona filtro` → `1.0.0` → `1.1.0`    |
| `BREAKING CHANGE:`         | MAJOR                | `feat!: remove API antiga` → `1.0.0` → `2.0.0` |
| `refactor:`, `docs:`, etc. | Nenhum (sem release) | Apenas desenvolvimento                         |

**Ferramentas de automação**:

- **semantic-release**: Automatiza versionamento baseado em commits
- **standard-version**: Gera changelog e versiona automaticamente
- **commitizen**: Ajuda a escrever commits no padrão

### 📊 Exemplo de Histórico de Versões

```
v0.1.0 - 2023-01-15 - Primeira versão de desenvolvimento
v0.2.0 - 2023-02-20 - Adiciona funcionalidade X
v0.3.0 - 2023-03-10 - Adiciona funcionalidade Y
v1.0.0 - 2023-04-01 - Primeira versão estável
v1.0.1 - 2023-04-05 - Corrige bug crítico
v1.1.0 - 2023-05-15 - Adiciona integração com API Z
v1.1.1 - 2023-05-20 - Corrige problema de performance
v1.2.0 - 2023-06-10 - Adiciona dashboard de analytics
v2.0.0-beta.1 - 2023-07-01 - Preview da versão 2.0
v2.0.0 - 2023-08-01 - Nova arquitetura (breaking changes)
```

### ✅ Boas Práticas

1. **Documente mudanças**: Mantenha um CHANGELOG.md atualizado
2. **Comunique breaking changes**: Avise usuários com antecedência
3. **Deprecie antes de remover**: Marque funcionalidades como deprecated em MINOR, remova em MAJOR
4. **Use tags anotadas**: Incluem mensagem e metadados
5. **Automatize quando possível**: Use ferramentas de CI/CD

### ❌ Erros Comuns

- ❌ Pular de `1.0.0` para `1.2.0` (pulou 1.1.0)
- ❌ Usar `v1.0` em vez de `v1.0.0` (versão incompleta)
- ❌ Fazer breaking change em MINOR ou PATCH
- ❌ Não documentar mudanças no CHANGELOG
- ❌ Versionar sem testar adequadamente

### 🔗 Recursos

- [Especificação SemVer 2.0.0 (PT-BR)](https://semver.org/lang/pt-BR/)
- [Conventional Commits](https://www.conventionalcommits.org/pt-br/)
- [Keep a Changelog](https://keepachangelog.com/pt-BR/)

---

## 10. Configuração do Git

### Configurações principais

```shell script
git config --global user.name "Seu Nome"
git config --global user.email "seu@email.com"
git config --list
```

#### Níveis de configuração

| Nível  | Escopo                       | Arquivo        |
|--------|------------------------------|----------------|
| system | Todos os usuários da máquina | /etc/gitconfig |
| global | Usuário atual                | ~/.gitconfig   |
| local  | Repositório específico       | .git/config    |

Configurações locais sempre têm precedência sobre as globais e sistêmicas.

---

## 11. Integridade e Segurança

O Git garante integridade através dos **hashes SHA-1**:

- Qualquer alteração em um arquivo ou metadado muda o hash.
- Isso impede corrupção ou manipulação silenciosa do histórico.

Além disso, o Git pode **assinar commits e tags** (com GPG ou SSH), permitindo verificar a autoria de forma criptográfica.

> 📝 **Nota**: A partir do Git 2.29, há suporte experimental para SHA-256, oferecendo maior segurança que SHA-1. O SHA-1 continua sendo o padrão.

---

## 12. Arquivos .gitignore e .gitattributes

**.gitignore**

Define **quais arquivos ou pastas o Git deve ignorar**, evitando poluição no repositório.

Exemplo:

### Ignora arquivos temporários e de build

```.gitignore (gitignore)
*.log
.env
.DS_Store
node_modules/
```

#### Exemplos por tecnologia:

**Node.js/JavaScript**:

```.gitignore (gitignore)
node_modules/
npm-debug.log
.env
dist/
```

**Python**:

```.gitignore (gitignore)
__pycache__/
*.pyc
venv/
.pytest_cache/
```

**Java**:

```.gitignore (gitignore)
target/
*.class
*.jar
.idea/
```

**.gitattributes**

Define como o Git deve tratar certos arquivos, útil para:

- Normalizar quebras de linha (EOL).
- Definir merges específicos.
- Marcar arquivos binários.

Exemplo:

```textmate
* text=auto
*.jpg binary
```

---

## 13. Princípio da História Imutável

O Git é *append-only*: o histórico é **acrescido**, não modificado.
Cada commit é **imutável**, e novos commits constroem uma nova linha de tempo.

Reescrever histórico (rebase, commit --amend, etc.) deve ser feito **somente** antes de compartilhar uma branch com outros.

> ⚠️ **ATENÇÃO**: Nunca faça rebase de commits que já foram compartilhados!

---

## 14. Vocabulário Essencial

| Termo             | Significado                                      |
|-------------------|--------------------------------------------------|
| Repository (repo) | Conjunto versionado de arquivos e histórico.     |
| Clone             | Cópia completa do repositório.                   |
| Commit            | Snapshot + metadados.                            |
| Branch            | Ponteiro móvel para um commit.                   |
| Tag               | Ponteiro fixo (geralmente para versões).         |
| HEAD              | Referência para o commit atual.                  |
| Remote            | Repositório hospedado remotamente (ex.: origin). |
| Fetch/Pull/Push   | Sincronização local ↔ remoto.                    |
| Merge/Rebase      | Integração de diferentes linhas de histórico.    |

---

## 15. Resumo Visual

```
           ┌───────────────────────┐
           │     Working Tree      │
           │ (Arquivos editáveis)  │
           └──────────┬────────────┘
                      │ git add
           ┌──────────▼────────────┐
           │     Staging Area      │
           │ (Index / preparação)  │
           └──────────┬────────────┘
                      │ git commit
           ┌──────────▼────────────┐
           │   Local Repository    │
           │ (.git / histórico)    │
           └──────────┬────────────┘
                      │ git push / pull
           ┌──────────▼────────────┐
           │  Remote Repository    │
           │ (GitHub, GitLab etc.) │
           └───────────────────────┘
```

---

## 16. Comandos Úteis para Diagnóstico

| Comando             | Descrição                                  |
|---------------------|--------------------------------------------|
| `git status`        | Mostra estado atual do working directory   |
| `git log`           | Exibe histórico de commits                 |
| `git log --oneline` | Histórico resumido em uma linha por commit |
| `git diff`          | Mostra diferenças não staged               |
| `git diff --staged` | Mostra diferenças na staging area          |
| `git show <commit>` | Exibe detalhes de um commit específico     |
| `git reflog`        | Histórico de movimentações do HEAD         |

---

## 17. Glossário Bilíngue

| Português          | Inglês            | Contexto                          |
|--------------------|-------------------|-----------------------------------|
| Ramificação        | Branch            | Linha de desenvolvimento paralela |
| Confirmação        | Commit            | Registro de mudança               |
| Mesclar            | Merge             | Unir branches                     |
| Rebasear           | Rebase            | Reescrever histórico              |
| Área de preparação | Staging Area      | Index antes do commit             |
| Árvore de trabalho | Working Tree      | Arquivos editáveis                |
| Repositório remoto | Remote Repository | Servidor central (GitHub, etc.)   |

---

## 18. Perguntas Frequentes

**P: Qual a diferença entre `git pull` e `git fetch`?**  
R: `git fetch` baixa as mudanças mas não as aplica. `git pull` = `git fetch` + `git merge`.

**P: Posso desfazer um commit?**  
R: Sim, com `git reset` (local) ou `git revert` (seguro para histórico compartilhado).

**P: O que fazer em caso de conflito de merge?**  
R: Editar os arquivos conflitantes, resolver as marcações, fazer `git add` e `git commit`.

---

## 19. Dicas de Resolução de Problemas

### Erro: "fatal: not a git repository"

**Solução**: Você não está em um diretório Git. Execute `git init` ou navegue até um repo existente.

### Erro: "Your branch is ahead of 'origin/main' by X commits"

**Solução**: Execute `git push` para enviar seus commits locais ao remoto.

### Arquivos que não deveriam estar sendo rastreados

**Solução**:

1. Adicione-os ao `.gitignore`
2. Execute `git rm --cached <arquivo>`
3. Faça commit da mudança

---

## 20. Referências e Recursos Adicionais

- [Documentação Oficial do Git (EN)](https://git-scm.com/doc)
- [Git Book em Português](https://git-scm.com/book/pt-br/v2)
- [Conventional Commits](https://www.conventionalcommits.org/pt-br/)
- [Versionamento Semântico 2.0.0](https://semver.org/lang/pt-BR/)
- [GitHub Skills](https://skills.github.com/)
- [Git Flow](https://nvie.com/posts/a-successful-git-branching-model/)
- [GitHub Flow](https://docs.github.com/pt/get-started/quickstart/github-flow)
- [Keep a Changelog](https://keepachangelog.com/pt-BR/)

---
