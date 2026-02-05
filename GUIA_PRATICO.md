# 🚀 GUIA PRÁTICO - COMO ENTREGAR ESTE PROJETO

## Passo 1: Preparar o Repositório GitHub

### 1.1 Criar Repositório

1. Abra https://github.com/new
2. Preencha:
   - **Repository name**: `portfolio-devops`
   - **Description**: `Portfolio com CI/CD - Infraestrutura Ágil com Práticas DevOps`
   - **Public** (obrigatório para GitHub Pages)
3. Clicar `Create repository`

### 1.2 Copiar URL do Repositório

Após criar, copiar a URL: `https://github.com/SEU_USUARIO/portfolio-devops.git`

---

## Passo 2: Fazer Upload dos Arquivos

### 2.1 Via Git (Recomendado)

```bash
# Navegue até a pasta do projeto
cd "c:\Users\rpv\Desktop\RPV\Trabalho - erick"

# Inicializar git (se não existir)
git init

# Adicionar todos os arquivos
git add .

# Fazer commit inicial
git commit -m "Initial commit: Portfolio DevOps com CI/CD"

# Adicionar remote
git remote add origin https://github.com/SEU_USUARIO/portfolio-devops.git

# Renomear branch para main (se necessário)
git branch -M main

# Fazer push para GitHub
git push -u origin main
```

### 2.2 Via GitHub Web (Alternativo)

1. Ir para repositório no GitHub
2. Clicar `Add file` → `Upload files`
3. Arrastar todos os arquivos
4. Commit message: "Initial commit: Portfolio DevOps"
5. Clicar `Commit changes`

---

## Passo 3: Ativar GitHub Pages

1. Ir para repositório
2. Clicar em `Settings`
3. Na sidebar, clicar em `Pages`
4. Em "Build and deployment":
   - **Source**: `Deploy from a branch`
   - **Branch**: `main` / folder `/ (root)`
5. Clicar `Save`
6. Aguardar 1-2 minutos

**Resultado**: URL gerada em "Your GitHub Pages site is published at: `https://seu-usuario.github.io/portfolio-devops/`"

---

## Passo 4: Configurar Proteção de Branch

### 4.1 Acessar Configurações

1. Repositório → `Settings`
2. Na sidebar → `Branches`
3. Clicar `Add rule`

### 4.2 Preencher Regra

**Branch name pattern**:
```
main
```

### 4.3 Ativar Proteções

✅ Marcar as seguintes opções:

- [ ] **Require a pull request before merging**
- [ ] **Require status checks to pass before merging**
  - Selecionar `validate` (aparecerá após primeiro PR)
- [ ] **Require branches to be up to date before merging**
- [ ] **Dismiss stale pull request approvals when new commits are pushed**

### 4.4 Bloquear Push Direto

✅ Marcar:

- [ ] **Restrict who can push to matching branches**
  - Deixar em branco (bloqueia todos exceto admins)

### 4.5 Salvar

Clicar `Create` e depois `Save changes`

---

## Passo 5: Adicionar Colaborador

### 5.1 Ir para Configurações

1. Repositório → `Settings`
2. Na sidebar → `Collaborators`
3. Clicar `Add people`

### 5.2 Adicionar Usuário

1. Digitar: `09116428-collab`
2. Selecionar da lista
3. Escolher role: **Maintain** (recomendado)
4. Clicar `Add`

**Resultado**: Usuário receberá convite por email

---

## Passo 6: Testar a Pipeline de CI

### 6.1 Criar Branch de Teste

```bash
git checkout -b test/validar-pipeline
```

### 6.2 Fazer Alteração Teste (Opcional)

```bash
# Adicionar um comentário válido (não quebra a CI)
echo "<!-- Este é um comentário válido -->" >> index.html

git add .
git commit -m "test: validar pipeline CI"
git push origin test/validar-pipeline
```

### 6.3 Criar Pull Request

1. Ir para repositório no GitHub
2. Clicar `Compare & pull request`
3. Escrever título: "Test: Validar CI Pipeline"
4. Clicar `Create pull request`

### 6.4 Assistir CI Executar

1. Na aba `Checks`, ver a pipeline executando
2. Aguardar conclusão (1-2 minutos)
3. Ver status final: ✅ Passed

---

## Passo 7: Gerar Screenshots Solicitados

### 📸 Screenshot 1: CI Falhando (Status Vermelho)

**Propósito**: Demonstrar que a pipeline detecta erros

**Procedimento**:

1. Criar nova branch:
   ```bash
   git checkout -b test/erro-intencional
   ```

2. Remover index.html:
   ```bash
   mv index.html index.html.bak
   git add -u
   git commit -m "🔴 TEST: remover index.html para demonstrar falha"
   git push origin test/erro-intencional
   ```

3. No GitHub:
   - Ir para repositório
   - Clicar `Compare & pull request`
   - Clicar `Create pull request`

4. Aguardar e ver:
   - Na aba `Checks`
   - Status: ❌ **FAILED**
   - Mensagem vermelha

5. **Tirar PrintScreen**:
   - Seção "Checks" em vermelho
   - Mostrando erro "index.html não encontrado"

6. Recuperar arquivo:
   ```bash
   mv index.html.bak index.html
   git add .
   git commit -m "✅ Fix: restaurar index.html"
   git push origin test/erro-intencional
   ```

---

### 📸 Screenshot 2: Deploy Concluído com Sucesso

**Propósito**: Demonstrar que o CD funciona

**Procedimento**:

1. Fazer merge da branch de teste:
   ```bash
   git checkout main
   git pull origin main
   git merge test/validar-pipeline
   git push origin main
   ```

2. No GitHub:
   - Ir para repositório
   - Clicar na aba `Actions`
   - Ver workflow `CD - Deploy para GitHub Pages`
   - Clicar no workflow mais recente

3. Ver job `build-and-deploy`:
   - Status: ✅ **PASSED**
   - Scroll para step "Deploy para GitHub Pages"
   - Ver mensagem: "✅ DEPLOY REALIZADO COM SUCESSO"
   - Ver URL do deployment

4. **Tirar PrintScreen**:
   - Mostrando job em verde ✅
   - Com URL do site visível

---

### 📸 Screenshot 3: GitHub Pages Ativo

**Procedimento**:

1. Repositório → `Settings`
2. Scroll para `Pages`
3. Ver mensagem:
   ```
   Your site is published at 
   https://seu-usuario.github.io/portfolio-devops/
   ```

4. **Tirar PrintScreen**:
   - Seção GitHub Pages
   - Com URL visível

---

### 📸 Screenshot 4: Badge de Status

**Procedimento**:

1. Ir para repositório
2. Ver README.md (deve abrir automaticamente)
3. Scroll para topo
4. Ver badge em Markdown:
   ```
   ![CI - Integração Contínua](...)
   ```

5. O badge mostra status atual (✅ passing ou ❌ failing)

6. **Tirar PrintScreen**:
   - Topo do README
   - Com badge visível

---

## Passo 8: Limpar Branches de Teste

```bash
# Deletar branches locais de teste
git branch -d test/validar-pipeline
git branch -d test/erro-intencional

# Deletar branches remotas
git push origin --delete test/validar-pipeline
git push origin --delete test/erro-intencional
```

---

## Passo 9: Verificar URL Final

1. Ir para: `https://seu-usuario.github.io/portfolio-devops/`
2. Verificar se site funciona
3. Ver navbar, seções, projetos, imagens
4. Testar links de redes sociais (devem abrir)
5. Verificar responsividade (abrir no mobile também)

---

## ✅ Checklist Final

Antes de entregar, garantir que:

- [ ] Repositório está público
- [ ] index.html existe na raiz
- [ ] style.css existe e está carregando
- [ ] Imagens carregam corretamente
- [ ] Badge de CI aparece no README
- [ ] GitHub Pages está ativo
- [ ] Branch main está protegida
- [ ] 09116428-collab foi adicionado
- [ ] CD workflow já executou com sucesso
- [ ] Site é acessível publicamente
- [ ] Screenshots foram tirados e salvos

---

## 📚 Arquivos Entregues

| Arquivo | Descrição |
|---------|-----------|
| `index.html` | Portfólio profissional (obrigatório) |
| `style.css` | Estilos responsivos |
| `images/projeto-1.png` | Imagem 1 otimizada |
| `images/projeto-2.png` | Imagem 2 otimizada |
| `images/projeto-3.png` | Imagem 3 otimizada |
| `.github/workflows/ci.yml` | Pipeline CI com 6 validações |
| `.github/workflows/cd.yml` | Pipeline CD com deploy automático |
| `README.md` | Documentação completa com badge |
| `ENTREGA_FINAL.md` | Este resumo |

---

## 🆘 Troubleshooting

### Problema: GitHub Pages não funciona

**Solução**:
1. Verificar se repositório é **public**
2. Verificar se `index.html` está na **raiz**
3. Ir para `Settings` → `Pages` e ativar
4. Aguardar 2-3 minutos

### Problema: CI não roda em Pull Request

**Solução**:
1. Verificar se arquivo `.github/workflows/ci.yml` existe
2. Verificar se está na estrutura correta de pastas
3. Fazer novo push para disparar
4. Ir para `Actions` e verificar se aparece

### Problema: Não consigo fazer merge

**Solução**:
1. Verificar se branch `main` está protegida (Settings → Branches)
2. Esperar a CI terminar (deve ficar ✅)
3. Se CI falhar, corrigir o código
4. Fazer novo push para re-rodar CI

### Problema: Badge mostra "Unknown"

**Solução**:
1. Aguardar primeira execução da CI
2. Badge atualiza automaticamente após
3. Pode levar 1-2 minutos

---

## 🎓 Resumo do Que Você Aprendeu

✅ Como criar pipelines CI/CD com GitHub Actions
✅ Como validar código automaticamente
✅ Como proteger branches em produção
✅ Como fazer deploy contínuo
✅ Como configurar notificações
✅ Como usar matrix strategy
✅ Como hostar site estático com GitHub Pages

---

## 📞 Contato para Dúvidas

Se encontrar erros na pipeline, verificar:
1. Logs em `Actions` → workflow → step específico
2. Se `index.html` está na raiz (obrigatório)
3. Se não há TODO, FIXME, senha ou password no código
4. Se imagens referenciadas existem em `images/`

---

**Boa sorte na entrega! 🚀**

Este projeto garante **NOTA MÁXIMA** se todos os steps forem seguidos corretamente.
