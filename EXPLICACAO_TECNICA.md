# 🔧 EXPLICAÇÃO TÉCNICA DOS WORKFLOWS

## 📋 Índice

1. [CI - Integração Contínua](#ci---integração-contínua)
2. [CD - Entrega Contínua](#cd---entrega-contínua)
3. [Matrix Strategy](#matrix-strategy)
4. [Permissões](#permissões)
5. [Triggers](#triggers)

---

## CI - Integração Contínua

### O que é CI?

CI significa **Continuous Integration** (Integração Contínua). É um processo que:
- ✅ Valida código antes de ser mesclado
- ✅ Garante qualidade automáticamente
- ✅ Bloqueia merge se testes falharem

### Quando Roda?

```yaml
on:
  pull_request:
    branches:
      - main
```

**Tradução**: "Execute sempre que houver um Pull Request para a branch `main`"

### Ambiente de Execução

```yaml
runs-on: ubuntu-latest
```

**O que é?**: Máquina Linux (Ubuntu) fornecida pelo GitHub onde rodam os testes.

### As 6 Validações Executadas

#### 1️⃣ Checkout do Repositório

```yaml
uses: actions/checkout@v4
```

**O que faz**: Baixa o código do Pull Request para a máquina de testes.

**Por quê?**: A máquina precisa ter acesso aos arquivos para validar.

---

#### 2️⃣ Verificação Imediata: index.html

```bash
if [ ! -f "index.html" ]; then
    echo "❌ ERRO CRÍTICO: arquivo index.html não encontrado!"
    exit 1
fi
```

**O que faz**: 
- Verifica se arquivo `index.html` existe
- Se NÃO existir → Falha imediatamente
- Se existir → Continua

**Por quê?**: GitHub Pages **obrigatoriamente** precisa de `index.html` na raiz.

**Erro comum**: Renomear para `index-teste.html` ou `home.html` quebra isso.

---

#### 3️⃣ HTML Linting com htmlhint

```bash
npm install -g htmlhint
htmlhint index.html
```

**O que faz**:
- Instala ferramenta de validação HTML
- Valida sintaxe e boas práticas
- Encontra tags mal fechadas, etc

**Exemplo de erro detectado**:
```html
<div>
  <p>Parágrafo sem fechar
</div>
```

**Resultado**: ❌ FAILED - tag `<p>` não fechada

---

#### 4️⃣ Verificação de Tamanho de Arquivos

```bash
LARGE_FILES=$(find . -type f -size +500k ! -path "./.git/*")
if [ -n "$LARGE_FILES" ]; then
    exit 1
fi
```

**O que faz**:
- Procura por arquivos maiores que 500KB
- Ignora pasta `.git/` (não contém seus arquivos)
- Falha se encontrar algo

**Por quê?**:
- GitHub Pages tem limite de tamanho
- Imagens grandes deixam site lento
- Melhor otimizar antes

**Exemplo**:
```
projeto-1.png (2MB) → ❌ FALHA
projeto-1.png (300KB) → ✅ PASSA
```

---

#### 5️⃣ Bloqueio de Palavras Proibidas

```bash
grep -r "TODO" . --exclude-dir=.git
grep -r "FIXME" .
grep -r "senha" .
grep -r "password" .
```

**O que faz**:
- Procura por palavras em TODO comentários
- Impede envio acidental de `<!-- TODO: fazer depois -->`
- Bloqueia palavras sensíveis como "senha" ou "password"

**Por quê?**:
- TODO indica código incompleto
- Nunca deve ir para produção
- Passwords nunca devem estar em código aberto

**Exemplo de falha**:
```html
<!-- TODO: implementar modal --> ❌ FALHA
<!-- Este é um comentário normal --> ✅ PASSA
```

---

#### 6️⃣ Validação de Links

```bash
MISSING_HREF=$(grep -o '<a[^>]*>' index.html | grep -v 'href=')
if [ -n "$MISSING_HREF" ]; then
    exit 1
fi
```

**O que faz**:
- Procura por tags `<a>` sem atributo `href`
- Garante que todos os links estão configurados
- Verifica se URLs estão válidas

**Exemplo**:
```html
<a href="https://github.com">GitHub</a> ✅ PASSA
<a>Link quebrado</a> ❌ FALHA (sem href)
```

---

#### 7️⃣ Validação de Imagens

```bash
MISSING_SRC=$(grep -o '<img[^>]*>' index.html | grep -v 'src=')
if [ -n "$MISSING_SRC" ]; then
    exit 1
fi

# Para cada imagem, verifica se arquivo existe
while IFS= read -r line; do
    SRC=$(echo "$line" | grep -o 'src="[^"]*"')
    if [ ! -f "$SRC" ]; then
        exit 1
    fi
done
```

**O que faz**:
1. Procura por `<img>` sem `src`
2. Para cada imagem, verifica se arquivo existe
3. Falha se imagem não existir

**Exemplo**:
```html
<img src="images/projeto-1.png" alt=""> ✅ PASSA (arquivo existe)
<img src="images/inexistente.png" alt=""> ❌ FALHA (arquivo não existe)
<img alt=""> ❌ FALHA (sem src)
```

---

### Resultado da CI

Se **TUDO passar**:
```
✅ index.html encontrado
✅ HTML válido
✅ Sem arquivos grandes
✅ Sem TODOs/FIXMEs/senhas
✅ Links completos
✅ Imagens existem
```

**→ Botão de Merge fica VERDE**

Se **ALGO falhar**:
```
❌ Validação falhou
```

**→ Botão de Merge fica VERMELHO (bloqueado)**

---

## CD - Entrega Contínua

### O que é CD?

CD significa **Continuous Deployment** (Entrega Contínua). É um processo que:
- 🚀 Publica automaticamente após validação
- ⚡ Sem intervenção humana
- 📱 Website fica online em segundos

### Quando Roda?

```yaml
on:
  push:
    branches:
      - main
```

**Tradução**: "Execute quando houver PUSH (alteração) na branch `main`"

### Permissões Requeridas

```yaml
permissions:
  contents: read      # Pode ler arquivos do repositório
  pages: write        # Pode escrever no GitHub Pages
  id-token: write     # Pode usar token de identidade
```

**Por quê cada uma?**

| Permissão | Por quê |
|-----------|---------|
| `contents: read` | Precisa ler `index.html` e demais arquivos |
| `pages: write` | Precisa de permissão para publicar site |
| `id-token: write` | Segurança: Token temporário para autenticação |

### Etapas do Deploy

#### Passo 1: Checkout

```yaml
uses: actions/checkout@v4
```

**O que faz**: Baixa código mais recente da branch `main`

---

#### Passo 2: Setup Pages

```yaml
uses: actions/configure-pages@v4
```

**O que faz**: Configura ambiente do GitHub Pages

**Por quê?**: GitHub Pages precisa de setup inicial

---

#### Passo 3: Validação

```bash
if [ ! -f "index.html" ]; then
    exit 1
fi
```

**O que faz**: Dupla verificação (também valida em CD)

**Por quê?**: Segurança - mesmo que CI tenha passado, verifica novamente

---

#### Passo 4: Upload de Artefatos

```yaml
uses: actions/upload-pages-artifact@v3
with:
  path: '.'
```

**O que faz**:
- Coleta todos os arquivos (`.` = pasta atual)
- Prepara para deploy
- Armazena em servidor temporário

**Path '.'**: Significa "enviar tudo desta pasta"

---

#### Passo 5: Deploy

```yaml
uses: actions/deploy-pages@v4
id: deployment
```

**O que faz**:
- Publica arquivos no GitHub Pages
- Gera URL pública
- Site fica acessível

**ID 'deployment'**: Captura URL para usar depois

---

#### Passo 6: Notificação de Sucesso

```yaml
if: success()
run: |
  echo "✅ DEPLOY REALIZADO COM SUCESSO"
  echo "📍 URL do site: ${{ steps.deployment.outputs.page_url }}"
```

**O que faz**: Se tudo passou, mostra mensagem de sucesso com URL

---

#### Passo 7: Notificação de Falha

```yaml
if: failure()
run: |
  echo "❌ ERRO NO DEPLOY"
  exit 1
```

**O que faz**: Se algo quebrou, avisa

---

### Fluxo Completo CD

```
1. Código é merged na main
   ↓
2. GitHub detecta push
   ↓
3. CD workflow inicia
   ↓
4. Baixa código
   ↓
5. Valida index.html
   ↓
6. Sobe arquivos para Pages
   ↓
7. Publica site
   ↓
8. Envia mensagem de sucesso
   ↓
✅ Site está ONLINE
```

**Tempo total**: ~30-60 segundos

---

## Matrix Strategy

### O que é?

Matrix é uma estratégia para rodar o **mesmo job em múltiplas combinações** de ambiente.

### Configuração

```yaml
strategy:
  matrix:
    node-version: [18.x, 20.x]
```

**Tradução**: "Execute este job 2 vezes:
- Uma com Node 18.x
- Uma com Node 20.x"

### Resultado

Seu workflow roda em PARALELO:

```
┌─────────────────────┬─────────────────────┐
│  Node 18.x Job      │  Node 20.x Job      │
│  ✅ Validação HTML  │  ✅ Validação HTML  │
│  ✅ Linting         │  ✅ Linting         │
│  ✅ Arquivos OK     │  ✅ Arquivos OK     │
│  ✅ Sem palavras... │  ✅ Sem palavras... │
└─────────────────────┴─────────────────────┘
        ~2 min              ~2 min
```

### Por quê?

✅ **Compatibilidade**: Garante que código funciona em múltiplas versões
✅ **Confiabilidade**: Não depende de uma versão específica
✅ **Profissionalismo**: Padrão em empresas reais

### Acesso aos Valores

No workflow, use `${{ matrix.node-version }}`:

```yaml
- name: Setup Node.js v${{ matrix.node-version }}
  uses: actions/setup-node@v4
  with:
    node-version: ${{ matrix.node-version }}
```

**Exemplo de execução**:
- Primeira run: `${{ matrix.node-version }}` = `18.x`
- Segunda run: `${{ matrix.node-version }}` = `20.x`

---

## Permissões

### Por que Usar permissions?

Em GitHub Actions, por **segurança**, workflows têm **permissões limitadas** por padrão.

Se você tenta fazer algo sem permissão:
```
❌ Error: GitHub Actions doesn't have permission to write to GitHub Pages
```

### Permissões Específicas do CD

```yaml
permissions:
  contents: read      # Ler arquivos (necessário)
  pages: write        # Publicar site (necessário)
  id-token: write     # Token OIDC (segurança)
```

### O que Cada Uma Permite

| Permissão | Permite | Bloqueado Sem |
|-----------|---------|---------------|
| `contents: read` | Ler código | ❌ Sem acesso aos arquivos |
| `pages: write` | Publicar GitHub Pages | ❌ Publish falha |
| `id-token: write` | Usar OIDC token | ⚠️ Segurança reduzida |

### Melhor Prática

✅ **Sempre especifique as permissões necessárias**
✅ **Use `read` quando possível** (menos risco)
✅ **Use `write` apenas quando necessário**

---

## Triggers

### CI Trigger

```yaml
on:
  pull_request:
    branches:
      - main
```

**O que acontece**:
- ✅ Pull Request aberto para `main`
- ✅ Novo commit em PR existente
- ❌ Push direto na `main` (CI não roda)
- ❌ PR para outras branches

**Motivo**: Validar antes de merge, não depois

---

### CD Trigger

```yaml
on:
  push:
    branches:
      - main
```

**O que acontece**:
- ✅ Push para `main` (após merge de PR)
- ✅ Commit direto na `main` (se permitido)
- ❌ Push para outras branches
- ❌ Pull Requests (só roda CD após merge)

**Motivo**: Publicar código já validado

---

## Fluxo Completo: CI + CD

```
┌─────────────────────────────────────────────────────────┐
│ 1. Desenvolvedor cria feature branch                    │
│    $ git checkout -b feature/nova-feature               │
└─────────────────────────────────────────────────────────┘
                           ↓
┌─────────────────────────────────────────────────────────┐
│ 2. Faz alterações e push                                │
│    $ git push origin feature/nova-feature               │
└─────────────────────────────────────────────────────────┘
                           ↓
┌─────────────────────────────────────────────────────────┐
│ 3. Abre Pull Request no GitHub                          │
│    "Adicionar nova seção"                               │
└─────────────────────────────────────────────────────────┘
                           ↓
┌─────────────────────────────────────────────────────────┐
│ 🔵 CI DISPARA (pull_request trigger)                    │
│                                                         │
│ ✅ Validação 1: index.html existe                       │
│ ✅ Validação 2: HTML válido                             │
│ ✅ Validação 3: Arquivos < 500KB                        │
│ ✅ Validação 4: Sem TODO/FIXME/senha                    │
│ ✅ Validação 5: Links válidos                           │
│ ✅ Validação 6: Imagens existem                         │
│                                                         │
│ Executado em: Node 18.x E 20.x (matrix)                │
│ Resultado: ✅ ALL CHECKS PASSED                         │
└─────────────────────────────────────────────────────────┘
                           ↓
┌─────────────────────────────────────────────────────────┐
│ 4. Botão de Merge fica VERDE ✅                         │
│    Desenvolvedor clica em "Squash and merge"            │
└─────────────────────────────────────────────────────────┘
                           ↓
┌─────────────────────────────────────────────────────────┐
│ 5. PR é mergeado para main                              │
│    $ git merge feature/nova-feature                     │
└─────────────────────────────────────────────────────────┘
                           ↓
┌─────────────────────────────────────────────────────────┐
│ 🟢 CD DISPARA (push to main trigger)                    │
│                                                         │
│ 1. Baixa código de main                                 │
│ 2. Valida index.html novamente                          │
│ 3. Sobe arquivos para GitHub Pages                      │
│ 4. Publica site automaticamente                         │
│ 5. Envia URL pública                                    │
│                                                         │
│ Resultado: ✅ DEPLOY REALIZADO COM SUCESSO              │
└─────────────────────────────────────────────────────────┘
                           ↓
┌─────────────────────────────────────────────────────────┐
│ 6. Site está ONLINE e acessível                         │
│    https://seu-usuario.github.io/portfolio-devops/      │
└─────────────────────────────────────────────────────────┘
```

---

## Resumo Técnico

| Aspecto | CI | CD |
|--------|----|----|
| **Trigger** | Pull Request | Push para main |
| **Objetivo** | Validar código | Publicar site |
| **Runner** | ubuntu-latest | ubuntu-latest |
| **Tempo** | 2-3 min | 1-2 min |
| **Falha bloqueia** | Merge | (já foi validado) |
| **Sucesso faz** | Permite merge | Publica site |
| **Matrix** | ✅ Node 18 + 20 | ❌ Não precisa |

---

**Compreender este fluxo é ESSENCIAL para dominar DevOps! 🚀**
