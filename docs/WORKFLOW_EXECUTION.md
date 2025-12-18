# ProtoWorks System (PWS) - Workflow de Execução de Tarefas

**Versão:** 1.1
**Data:** 17 de Dezembro de 2025
**Escopo:** Processo padronizado para execução, revisão, teste e deploy de tarefas

---

## Visão Geral

Este documento define o fluxo de trabalho obrigatório para execução de todas as tarefas do projeto PWS. O objetivo é garantir qualidade, alinhamento com requisitos e rastreabilidade.

---

## Estrutura de Branches (Simplificada)

```
                                    ┌─────────────┐
                                    │   master    │  ← Produção (releases)
                                    └──────┬──────┘
                                           │
                                    ┌──────▼──────┐
                                    │   stage     │  ← Integração + QA
                                    └──────┬──────┘
                                           │
              ┌────────────────────────────┼────────────────────────────┐
              │                            │                            │
       ┌──────▼──────┐              ┌──────▼──────┐              ┌──────▼──────┐
       │ feature/    │              │ feature/    │              │ feature/    │
       │ DB-001      │              │ BE-103      │              │ FE-101      │
       └─────────────┘              └─────────────┘              └─────────────┘
```

### Descrição das Branches

| Branch | Propósito | Proteção |
|--------|-----------|----------|
| `master` | Código em produção, apenas releases | Protegida, requer PR aprovado de `stage` |
| `stage` | Integração contínua + Homologação/QA | Protegida, requer PR aprovado de `feature/*` |
| `feature/*` | Desenvolvimento de tarefas | Não protegida, criada por dev |
| `hotfix/*` | Correções urgentes em produção | Merge direto em `master` e `stage` |
| `release/*` | Preparação de release | Criada de `stage`, merge em `master` |

### Nomenclatura de Branches

```
feature/[ID]-[descricao-curta]

Exemplos:
- feature/DB-001-alembic-setup
- feature/BE-103-auth-endpoints
- feature/FE-101-login-page
```

---

## Fluxo Completo de Execução

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         ETAPA 1: VALIDAÇÃO                                  │
│                         Responsável: Claude + Usuário                       │
├─────────────────────────────────────────────────────────────────────────────┤
│  • Apresentar descrição da tarefa ao usuário                                │
│  • Confirmar escopo e critérios de aceite                                   │
│  • Identificar dependências e riscos                                        │
│  • AGUARDAR APROVAÇÃO antes de prosseguir                                   │
└─────────────────────────────────────────────────────────────────────────────┘
                                     │
                                     ▼ (Aprovado)
┌─────────────────────────────────────────────────────────────────────────────┐
│                         ETAPA 2: BRANCH & IMPLEMENTAÇÃO                     │
│                         Responsável: Agente Especializado                   │
├─────────────────────────────────────────────────────────────────────────────┤
│  • Criar branch: git checkout -b feature/[ID]-[nome] (de stage)             │
│  • Implementar código                                                       │
│  • Commits atômicos com mensagens descritivas                               │
│  • Push para origin                                                         │
└─────────────────────────────────────────────────────────────────────────────┘
                                     │
                                     ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                         ETAPA 3: PULL REQUEST                               │
│                         Responsável: Agente que implementou                 │
├─────────────────────────────────────────────────────────────────────────────┤
│  • Abrir PR: feature/[ID] → stage                                           │
│  • Preencher template do PR                                                 │
│  • Vincular à tarefa/issue                                                  │
│  • Solicitar reviewers                                                      │
└─────────────────────────────────────────────────────────────────────────────┘
                                     │
                                     ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                         ETAPA 4: CODE REVIEW                                │
│                         Responsável: Outro Agente Especializado             │
├─────────────────────────────────────────────────────────────────────────────┤
│  • Backend → staff-backend-dev (instância diferente)                        │
│  • Frontend → staff-frontend-dev (instância diferente)                      │
│  • Aprovar ou Solicitar mudanças                                            │
│  • Se mudanças: dev corrige e push novos commits                            │
└─────────────────────────────────────────────────────────────────────────────┘
                                     │
                                     ▼ (Aprovado)
┌─────────────────────────────────────────────────────────────────────────────┐
│                         ETAPA 5: MERGE → STAGE                              │
│                         Responsável: Reviewer que aprovou                   │
├─────────────────────────────────────────────────────────────────────────────┤
│  • Squash and merge para stage                                              │
│  • Deletar branch feature                                                   │
│  • CI/CD executa testes automáticos                                         │
└─────────────────────────────────────────────────────────────────────────────┘
                                     │
                                     ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                         ETAPA 6: TESTES EM STAGE                            │
│                         Responsável: testing-specialist                     │
├─────────────────────────────────────────────────────────────────────────────┤
│  • Testes unitários e integração executam automaticamente (CI)              │
│  • testing-specialist valida cenários adicionais                            │
│  • Deploy automático para ambiente de homologação                           │
│  • Se falhar: abrir nova branch para correção                               │
└─────────────────────────────────────────────────────────────────────────────┘
                                     │
                                     ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                         ETAPA 7: QA + UAT EM STAGE                          │
│                         Responsável: testing-specialist + Usuário           │
├─────────────────────────────────────────────────────────────────────────────┤
│  • Testes E2E completos                                                     │
│  • Testes de regressão                                                      │
│  • Validação com usuário (UAT)                                              │
│  • Se bugs: fix em nova feature branch, PR para stage                       │
└─────────────────────────────────────────────────────────────────────────────┘
                                     │
                                     ▼ (Aprovado para produção)
┌─────────────────────────────────────────────────────────────────────────────┐
│                         ETAPA 8: PR → MASTER (RELEASE)                      │
│                         Responsável: Tech Lead / Usuário                    │
├─────────────────────────────────────────────────────────────────────────────┤
│  • Criar branch release/vX.Y.Z de stage                                     │
│  • Atualizar versão e CHANGELOG                                             │
│  • Abrir PR: release/vX.Y.Z → master                                        │
│  • Aprovar e fazer merge                                                    │
│  • Tag de versão criada automaticamente                                     │
│  • Deploy para produção                                                     │
└─────────────────────────────────────────────────────────────────────────────┘
                                     │
                                     ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                         ETAPA 9: CONCLUSÃO                                  │
│                         Responsável: Claude                                 │
├─────────────────────────────────────────────────────────────────────────────┤
│  • Atualizar status da tarefa (TodoWrite)                                   │
│  • Reportar ao usuário                                                      │
│  • Documentar lições aprendidas                                             │
│  • Prosseguir para próxima tarefa                                           │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Detalhamento das Etapas

### ETAPA 1: Validação com Usuário

**Objetivo:** Garantir alinhamento antes de iniciar qualquer implementação.

**Processo:**

1. Claude apresenta ao usuário:
   ```
   📋 TAREFA: [ID] - [Nome da Tarefa]

   📝 DESCRIÇÃO:
   [Descrição detalhada do que será implementado]

   ✅ CRITÉRIOS DE ACEITE:
   - [ ] Critério 1
   - [ ] Critério 2
   - [ ] Critério N

   🔗 DEPENDÊNCIAS:
   - [Tarefas que precisam estar concluídas]

   ⚠️ RISCOS IDENTIFICADOS:
   - [Riscos potenciais e mitigações]

   🛠️ ABORDAGEM TÉCNICA:
   [Breve explicação de como será implementado]

   🌿 BRANCH: feature/[ID]-[nome-curto]

   ⏱️ COMPLEXIDADE ESTIMADA: [XS/S/M/L/XL]

   Posso prosseguir com esta tarefa? (sim/não/ajustar)
   ```

2. Aguardar resposta do usuário:
   - **"sim"** → Prosseguir para Etapa 2
   - **"não"** → Cancelar ou selecionar outra tarefa
   - **"ajustar"** → Usuário fornece ajustes, Claude reapresenta

**Regra:** NUNCA iniciar implementação sem aprovação explícita do usuário.

---

### ETAPA 2: Branch e Implementação

**Objetivo:** Criar branch isolada e implementar a funcionalidade.

**Comandos Git:**

```bash
# Atualizar stage
git checkout stage
git pull origin stage

# Criar feature branch
git checkout -b feature/[ID]-[descricao-curta]

# Após implementação, commits atômicos
git add .
git commit -m "[ID] Descrição do que foi feito"

# Push para origin
git push -u origin feature/[ID]-[descricao-curta]
```

**Padrão de Commits:**

```
[ID] Tipo: Descrição curta

Corpo opcional com mais detalhes.

Exemplos:
- [DB-001] feat: Initialize Alembic configuration
- [BE-103] feat: Add login and logout endpoints
- [FE-101] feat: Create login page with form validation
- [BE-103] fix: Handle invalid token exception
- [FE-101] refactor: Extract form logic to custom hook
```

**Agentes por Tipo de Tarefa:**

| Prefixo | Tipo | Agente |
|---------|------|--------|
| DB-XXX | Database/Migrations | `staff-backend-dev` |
| BE-XXX | Backend/API | `staff-backend-dev` |
| FE-XXX | Frontend/UI | `staff-frontend-dev` |

---

### ETAPA 3: Pull Request

**Objetivo:** Solicitar revisão formal do código.

**Template de PR:**

```markdown
## 📋 Tarefa
[ID] - [Nome da Tarefa]

## 📝 Descrição
[O que foi implementado]

## 🔄 Tipo de Mudança
- [ ] Nova feature
- [ ] Bug fix
- [ ] Refatoração
- [ ] Documentação
- [ ] Configuração

## ✅ Checklist
- [ ] Código segue os padrões do projeto
- [ ] Self-review realizado
- [ ] Testes adicionados/atualizados
- [ ] Documentação atualizada (se necessário)

## 🧪 Como Testar
1. [Passo 1]
2. [Passo 2]
3. [Resultado esperado]

## 📸 Screenshots (se aplicável)
[Imagens da UI]

## 🔗 Links Relacionados
- Issue: #[número]
- Dependências: [PRs relacionados]
```

**Comando para criar PR via CLI:**

```bash
gh pr create \
  --base stage \
  --title "[ID] Descrição curta" \
  --body "Descrição do PR"
```

---

### ETAPA 4: Code Review

**Objetivo:** Garantir qualidade através de revisão por pares.

**Regra Fundamental:** O agente que implementou NÃO pode aprovar o próprio PR.

**Agentes Revisores:**

| Código Implementado Por | Revisor |
|------------------------|---------|
| `staff-backend-dev` (Backend) | `staff-backend-dev` (nova instância) |
| `staff-frontend-dev` (Frontend) | `staff-frontend-dev` (nova instância) |

**Checklist de Code Review:**

```markdown
## Revisão Geral
- [ ] Código resolve o problema descrito na tarefa
- [ ] Não há código duplicado ou desnecessário
- [ ] Nomenclatura clara e consistente
- [ ] Complexidade adequada (não over-engineered)

## Segurança
- [ ] Não há exposição de dados sensíveis
- [ ] Inputs validados e sanitizados
- [ ] Autenticação/autorização correta
- [ ] Sem vulnerabilidades OWASP Top 10

## Performance
- [ ] Queries otimizadas
- [ ] Sem N+1 queries
- [ ] Recursos liberados corretamente
- [ ] Sem memory leaks

## Testes
- [ ] Testes unitários presentes
- [ ] Testes cobrem casos principais
- [ ] Testes cobrem edge cases
- [ ] Todos os testes passando

## Backend Específico
- [ ] Schemas Pydantic corretos
- [ ] Tratamento de erros adequado
- [ ] Transações DB corretas
- [ ] Audit logging implementado
- [ ] Migrations reversíveis

## Frontend Específico
- [ ] Componentes reutilizáveis
- [ ] Estado gerenciado corretamente
- [ ] Loading/Error states
- [ ] Acessibilidade (ARIA)
- [ ] Responsividade
- [ ] TypeScript sem any
```

**Ações do Reviewer:**

- **Approve:** PR está pronto para merge
- **Request Changes:** Problemas que devem ser corrigidos
- **Comment:** Sugestões opcionais ou perguntas

**Se Request Changes:**

```bash
# Dev corrige na mesma branch
git add .
git commit -m "[ID] fix: Correções do code review"
git push

# Reviewer re-avalia
```

---

### ETAPA 5: Merge para Stage

**Objetivo:** Integrar código aprovado na branch de integração.

**Quem faz o merge:** O reviewer que aprovou o PR.

**Tipo de Merge:** Squash and Merge (commits limpos em stage)

```bash
# Via GitHub UI ou CLI
gh pr merge [PR_NUMBER] --squash --delete-branch
```

**Após o Merge:**

1. CI/CD executa automaticamente:
   - Lint
   - Testes unitários
   - Build
   - Cobertura de código
   - Deploy para ambiente de staging

2. Se CI falhar:
   - Abrir nova branch de fix
   - Corrigir e criar novo PR

---

### ETAPA 6: Testes em Stage

**Objetivo:** Validar integração com código existente.

**Agente:** `testing-specialist`

**Testes Automatizados (CI):**

```yaml
# .github/workflows/ci.yml
name: CI
on:
  push:
    branches: [stage]
  pull_request:
    branches: [stage]

jobs:
  test-backend:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Run pytest
        run: |
          cd backend
          pip install -r requirements.txt
          pytest --cov=app --cov-report=xml

  test-frontend:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Run vitest
        run: |
          cd frontend
          npm ci
          npm run test:coverage
```

**Testes Manuais pelo testing-specialist:**

```
Execute testes de integração para as features merged em stage:

FEATURES INCLUÍDAS:
- [ID-1] - [Nome]
- [ID-2] - [Nome]

CENÁRIOS A TESTAR:
1. [Cenário de integração 1]
2. [Cenário de integração 2]
3. [Cenário de regressão]

Reporte:
- Testes que passaram
- Testes que falharam
- Bugs encontrados
```

---

### ETAPA 7: QA + UAT em Stage

**Objetivo:** Validação completa antes de produção.

**Ambiente:** Homologação (staging)

**Responsáveis:**

- `testing-specialist`: Testes E2E e regressão
- **Usuário**: UAT (User Acceptance Testing)

**Checklist de QA:**

```markdown
## Testes Funcionais
- [ ] Todas as features funcionam conforme especificado
- [ ] Fluxos principais E2E validados
- [ ] Edge cases testados

## Testes de Regressão
- [ ] Features anteriores continuam funcionando
- [ ] Integrações não quebraram

## Testes de Performance
- [ ] Tempo de resposta aceitável
- [ ] Sem memory leaks
- [ ] Carga simulada (se aplicável)

## Testes de Segurança
- [ ] Scan de vulnerabilidades
- [ ] Testes de penetração básicos
- [ ] Validação de permissões

## UAT (Usuário)
- [ ] Fluxos de negócio validados
- [ ] UX aprovada
- [ ] Dados de teste verificados
```

**Se bugs encontrados:**

1. Documentar bug
2. Fix em nova branch `feature/fix-[bug-id]`
3. PR para stage → review → merge
4. Re-testar

---

### ETAPA 8: Release para Master

**Objetivo:** Deploy em produção.

**Processo:**

```bash
# Criar branch de release
git checkout stage
git pull
git checkout -b release/v1.0.0

# Atualizar versão
# - package.json (frontend)
# - pyproject.toml (backend)
# - CHANGELOG.md

git add .
git commit -m "chore: Bump version to v1.0.0"
git push -u origin release/v1.0.0

# Criar PR para master
gh pr create \
  --base master \
  --head release/v1.0.0 \
  --title "Release v1.0.0" \
  --body "## Changelog
  [Conteúdo do CHANGELOG]"
```

**Após Merge:**

```bash
# Tag automática (ou manual)
git checkout master
git pull
git tag -a v1.0.0 -m "Release v1.0.0"
git push origin v1.0.0

# Sincronizar stage com master (garantir paridade)
git checkout stage
git merge master
git push
```

**Deploy:** Automático via CI/CD para produção.

---

### ETAPA 9: Conclusão

**Objetivo:** Finalizar ciclo e preparar próxima iteração.

**Ações:**

1. **Atualizar TodoWrite:**
   - Marcar tarefa como `completed`

2. **Reportar ao Usuário:**
   ```
   ✅ TAREFA CONCLUÍDA: [ID] - [Nome]

   📁 ARQUIVOS CRIADOS/MODIFICADOS:
   - path/to/file1.py
   - path/to/file2.tsx

   🔀 PR: #[número] (merged)
   🌿 BRANCH: feature/[ID]-[nome] (deletada)

   🧪 COBERTURA DE TESTES: XX%

   📝 NOTAS:
   [Observações relevantes]

   ➡️ PRÓXIMA TAREFA SUGERIDA: [ID] - [Nome]

   Posso prosseguir para a próxima tarefa?
   ```

---

## Fluxo de Hotfix (Produção)

Para bugs críticos em produção:

```
┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│    master    │ ←── │  hotfix/xxx  │ ──→ │    master    │
└──────────────┘     └──────┬───────┘     └──────────────┘
                            │
                            ▼
                     ┌──────────────┐
                     │    stage     │  (cherry-pick ou merge)
                     └──────────────┘
```

```bash
# Criar hotfix de master
git checkout master
git pull
git checkout -b hotfix/critical-bug-fix

# Fix e commit
git add .
git commit -m "[HOTFIX] Fix critical bug"

# PR para master (revisão rápida)
gh pr create --base master --title "[HOTFIX] Fix critical bug"

# Após merge em master, também aplicar em stage
git checkout stage
git pull
git merge master  # ou cherry-pick específico
git push
```

---

## Configuração de Proteção de Branches

### GitHub Branch Protection Rules

**master:**
```
- Require pull request reviews: 1
- Require status checks: CI passing
- Require branches up to date: Yes
- Include administrators: Yes
- Allow force pushes: No
- Allow deletions: No
```

**stage:**
```
- Require pull request reviews: 1
- Require status checks: CI passing
- Dismiss stale reviews: Yes
- Allow force pushes: No
- Allow deletions: No
```

---

## Resumo Visual do Fluxo

```
TAREFA APROVADA
      │
      ▼
┌─────────────────┐
│ feature branch  │  ← Dev implementa
└────────┬────────┘
         │ PR
         ▼
┌─────────────────┐
│     stage       │  ← Review + Merge + Testes + QA
└────────┬────────┘
         │ Aprovado
         ▼
┌─────────────────┐
│    master       │  ← Produção
└─────────────────┘
```

---

## Regras Gerais

### Comunicação

1. **Sempre informar o usuário** sobre mudanças de etapa
2. **Nunca assumir** - perguntar quando houver dúvida
3. **Reportar bloqueios** imediatamente
4. **PRs abertos** devem ser revisados em até 24h

### Qualidade

1. **Não pular etapas** - todas são obrigatórias
2. **Não aprovar PR próprio** - sempre outro agente/dev
3. **Testes são obrigatórios** - mínimo 70% cobertura
4. **Code review é obrigatório** - mesmo para mudanças pequenas
5. **Nunca fazer push direto** em branches protegidas

### Git

1. **Commits atômicos** - uma mudança lógica por commit
2. **Mensagens descritivas** - prefixo com ID da tarefa
3. **Branches curtas** - merge frequente, evitar branches longevas
4. **Squash on merge** - manter histórico limpo em stage

---

## Tratamento de Exceções

### Conflitos de Merge

```
⚠️ CONFLITO DE MERGE DETECTADO

Branch: feature/[ID]-[nome]
Conflito com: stage

Arquivos em conflito:
- path/to/file1.py
- path/to/file2.tsx

Ação: Resolver conflitos localmente e push novo commit.
```

### CI Falha Após Merge

```
❌ CI FALHOU EM STAGE

Build: #[número]
Motivo: [Descrição do erro]

Ação imediata necessária:
1. Abrir branch feature/fix-ci-[ID]
2. Corrigir problema
3. PR urgente para stage
```

### Rollback Necessário

```
🔄 ROLLBACK NECESSÁRIO

Motivo: [Bug crítico em produção]

Ação:
1. git revert [commit-hash]
2. PR de emergência para master
3. Investigar causa raiz
4. Fix em stage com novo ciclo
```

---

## Métricas de Acompanhamento

### Por PR
- Tempo até primeira revisão
- Número de iterações até aprovação
- Tempo total até merge

### Por Sprint
- PRs abertos vs merged
- Taxa de aprovação em primeiro review
- Bugs encontrados em stage
- Bugs que chegaram a produção

### Por Release
- Lead time (feature request → produção)
- Frequência de deploys
- Taxa de rollback
- Incidentes em produção

---

## Anexo: Complexidade de Tarefas

| Complexidade | Pontos | Tempo Estimado | Exemplo |
|--------------|--------|----------------|---------|
| XS | 1 | 1-2 horas | Schema Pydantic simples |
| S | 2 | 2-4 horas | CRUD endpoint |
| M | 3 | 4-8 horas | Service com lógica de negócio |
| L | 5 | 1-2 dias | State machine de protocolo |
| XL | 8 | 2-3 dias | Sistema de sync offline |

---

## Histórico de Revisões

| Versão | Data | Autor | Mudanças |
|--------|------|-------|----------|
| 1.0 | 2025-12-17 | Claude | Versão inicial com GitFlow completo |
| 1.1 | 2025-12-17 | Claude | Simplificado para 2 branches (stage + master) |

---

**Fim do Documento de Workflow**
