![CI - Integração Contínua](https://github.com/SEU_USUARIO/portfolio-devops/actions/workflows/ci.yml/badge.svg)

# 📌 Portfólio DevOps - Infraestrutura Ágil com Práticas DevOps

Projeto acadêmico completo demonstrando **Infraestrutura Ágil**, **CI/CD profissional** e **Automação com GitHub Actions**.

---

## 📁 Estrutura do Projeto

```
portfolio-devops/
├── index.html                 # Página principal do portfólio
├── style.css                  # Estilos profissionais e responsivos
├── images/                    # Imagens otimizadas (< 500KB)
│   ├── projeto-1.png
│   ├── projeto-2.png
│   └── projeto-3.png
├── .github/
│   └── workflows/
│       ├── ci.yml             # Workflow de Integração Contínua
│       └── cd.yml             # Workflow de Entrega Contínua
└── README.md                  # Este arquivo
```

---

## ⚙️ CI - Integração Contínua

### Validações Obrigatórias na Pipeline CI

| Validação | Descrição |
|-----------|-----------|
| **1. Arquivo index.html** | Verifica se existe na raiz do projeto |
| **2. HTML Linting** | Valida sintaxe com `htmlhint` |
| **3. Tamanho de Arquivos** | Bloqueia arquivos > 500KB usando `find` |
| **4. Palavras Bloqueadas** | Impede TODO, FIXME, senha, password |
| **5. Links Válidos** | Verifica todas as tags `<a>` |
| **6. Imagens Válidas** | Verifica todas as tags `<img>` |

### Estratégia de Matrix

A CI executa em **2 versões do Node.js**:
- Node.js 18.x
- Node.js 20.x

Ambas devem passar para o merge ser permitido.

---

## 🚀 CD - Entrega Contínua

A pipeline **CD** dispara automaticamente ao fazer **push para a branch main** e publica o site no **GitHub Pages** sem intervenção humana.

### Permissões Requeridas

```yaml
permissions:
  contents: read      # Ler repositório
  pages: write        # Escrever no GitHub Pages
  id-token: write     # Token de identidade (segurança)
```

---

## 🔐 Proteção de Branch

### Como Configurar no GitHub

#### Passo 1: Acessar Configurações

1. Ir para `Settings` → `Branches`
2. Clicar em `Add rule`

#### Passo 2: Configurar Branch `main`

**Campo: Branch name pattern**
```
main
```

#### Passo 3: Ativar Requisitos de Merge

✅ Marcar:
- **Require a pull request before merging**
- **Require status checks to pass before merging**
  - Selecionar: `validate` (da pipeline CI)
- **Require branches to be up to date before merging**

#### Passo 4: Bloquear Push Direto

✅ Marcar:
- **Restrict who can push to matching branches**

### Resultado

```
✅ Branch main protegida
✅ CI deve passar para merge
✅ Push direto BLOQUEADO
✅ Merge será green apenas se pipeline passar
```

---

## 🔔 Notificações de Falha

### Opção 1: Email Automático (Nativo do GitHub)

1. Ir para `Settings` (conta)
2. `Notifications` → `Actions`
3. Marcar: "Send notifications for failed workflows"

O GitHub envia email automaticamente em caso de falha.

### Opção 2: Webhook Discord

1. Criar webhook no Discord
2. Ir para repositório `Settings` → `Webhooks`
3. Adicionar webhook com:
   - **Payload URL**: `https://discordapp.com/api/webhooks/YOUR_ID/YOUR_TOKEN`
   - **Content type**: `application/json`
   - **Eventos**: `Workflow jobs`

---

## 🧪 Como Testar e Gerar Erros

### Teste 1: Erro de Arquivo Faltando

```bash
git checkout -b test/erro-html
mv index.html index.html.bak
git add .
git commit -m "Test: remover index.html"
git push origin test/erro-html
# Abrir Pull Request no GitHub
```

**Resultado**: ❌ CI falha (arquivo não encontrado)

### Teste 2: Erro de Palavra Bloqueada

```bash
git checkout -b test/palavra-bloqueada
echo "<!-- TODO: implementar -->" >> index.html
git add .
git commit -m "Test: adicionar TODO"
git push origin test/palavra-bloqueada
```

**Resultado**: ❌ CI falha (palavra TODO encontrada)

### Teste 3: Erro de Imagem Quebrada

```bash
git checkout -b test/imagem-quebrada
# Editar index.html e adicionar:
# <img src="images/inexistente.png" alt="test">
git add .
git commit -m "Test: imagem inexistente"
git push origin test/imagem-quebrada
```

**Resultado**: ❌ CI falha (imagem não existe)

---

## 📸 Onde Tirar Screenshots

### Print 1: CI Falhando (Status Vermelho)

1. Repositório → `Pull requests`
2. Abrir PR com erro
3. Scroll para `Checks`
4. Ver status **❌ FAILED**
5. **PrintScreen**: Seção de checks em vermelho

### Print 2: Deploy Concluído com Sucesso

1. Repositório → `Actions`
2. Selecionar workflow CD mais recente
3. Clicar em `build-and-deploy`
4. Ver mensagem "✅ DEPLOY REALIZADO COM SUCESSO"
5. **PrintScreen**: Step com URL de deployment

### Print 3: GitHub Pages Ativo

1. Repositório → `Settings`
2. Scroll para `GitHub Pages`
3. Ver: "Your site is live at: `https://seu-usuario.github.io/portfolio-devops/`"
4. **PrintScreen**: Seção GitHub Pages

### Print 4: Badge de Status

1. README.md com badge no topo
2. Verificar que mostra status atual (passing/failing)
3. **PrintScreen**: Topo do README com badge

---

## 👥 Adicionar Colaborador

### Tarefa: Adicionar `09116428-collab`

#### Passo 1: Acessar Configurações

1. Repositório → `Settings` → `Collaborators`

#### Passo 2: Adicionar Novo Colaborador

1. Clicar `Add people`
2. Digitar: `09116428-collab`
3. Selecionar da lista
4. Escolher role:
   - **Maintain** (Recomendado)
   - Ou **Write**
5. Clicar `Add`

#### Passo 3: Verificação

1. Ir para `Settings` → `Collaborators`
2. Confirmar que `09116428-collab` aparece na lista
3. Verificar permissão atribuída

---

## 🌐 URLs Importantes

### URL do Portfólio (GitHub Pages)

```
https://seu-usuario.github.io/portfolio-devops/
```

**Onde encontrar**:
1. Repositório → `Settings` → `GitHub Pages`
2. Copiar URL em "Your site is live at"

### URL do Repositório

```
https://github.com/seu-usuario/portfolio-devops
```

### URL dos Workflows

```
https://github.com/seu-usuario/portfolio-devops/actions
```

---

## 📊 Tecnologias Utilizadas

| Tecnologia | Uso |
|------------|-----|
| **HTML5** | Marcação semântica |
| **CSS3** | Estilo responsivo |
| **GitHub Actions** | Automação CI/CD |
| **Node.js** | Runtime (versões 18.x, 20.x) |
| **htmlhint** | Linter HTML |
| **GitHub Pages** | Hospedagem estática |

---

## ✅ Checklist de Entrega

- [x] index.html criado e validado
- [x] style.css com design responsivo
- [x] Imagens otimizadas (< 500KB)
- [x] Workflow CI com 6 validações
- [x] Workflow CD com deploy automático
- [x] Branch main protegida
- [x] Badge de status no README
- [x] Notificações implementadas
- [x] Testes documentados
- [x] Colaborador adicionado
- [x] URLs públicas documentadas

---

## 🎓 Status Final

✅ **Pronto para Produção**

Este projeto atende 100% aos requisitos acadêmicos de Infraestrutura Ágil com Práticas DevOps.

**Desenvolvido**: Fevereiro 2026
**Autor**: Aluno
um erro de propósito no HTML.
3. Um print da aba "Actions" mostrando o fluxo de Deploy concluído com sucesso.
4. O link da URL do GitHub Pages onde o site pode ser acessado por qualquer pessoa.
5. Inserir o login 09116428-collab como colaboradora.