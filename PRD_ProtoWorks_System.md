# ProtoWorks System (PWS) - Product Requirements Document

**Versao:** 1.1
**Data:** 17 de Dezembro de 2025 (Revisado)
**Autor:** Documento gerado via sessao de descoberta estruturada
**Stakeholder Principal:** EGT (Empresa de Comissionamento)

---

## Indice

1. [Sumario Executivo](#1-sumario-executivo)
2. [Visao e Objetivos do Projeto](#2-visao-e-objetivos-do-projeto)
3. [Personas e Papeis de Usuario](#3-personas-e-papeis-de-usuario)
4. [Requisitos Funcionais](#4-requisitos-funcionais)
5. [Maquinas de Estado do Ciclo de Vida dos Protocolos](#5-maquinas-de-estado-do-ciclo-de-vida-dos-protocolos)
6. [Recomendacoes de Modelo de Dados](#6-recomendacoes-de-modelo-de-dados)
7. [Requisitos Nao-Funcionais](#7-requisitos-nao-funcionais)
8. [Arquitetura Tecnica](#8-arquitetura-tecnica)
9. [Definicao do Escopo MVP](#9-definicao-do-escopo-mvp)
10. [Fases Futuras](#10-fases-futuras)
11. [Riscos e Mitigacoes](#11-riscos-e-mitigacoes)
12. [Apendice](#12-apendice)

---

## 1. Sumario Executivo

### 1.1 Contexto

O **ProtoWorks System (PWS)** e uma plataforma de gerenciamento de protocolos de comissionamento desenvolvida para a EGT, uma empresa especializada em comissionamento industrial. O sistema substitui processos baseados em papel e planilhas, digitalizando todo o ciclo de vida de protocolos em projetos de engenharia, construcao e manutencao.

### 1.2 Problema

Atualmente, o gerenciamento de protocolos de comissionamento e feito manualmente atraves de documentos em papel e planilhas Excel. Isso resulta em:
- Falta de rastreabilidade e auditoria
- Dificuldade de acompanhamento de status em tempo real
- Risco de perda de documentos e dados
- Ineficiencia operacional
- Dificuldade de coordenacao entre multiplas empresas (Montadora, Gerenciadora, Comissionadora)

### 1.3 Solucao

O PWS oferece uma plataforma digital completa que:
- Digitaliza todo o fluxo de protocolos C1, C2, C3 e C4
- Permite execucao de protocolos em campo via tablets (PWA)
- Garante rastreabilidade completa com logs de auditoria (quem, quando, IP)
- Implementa assinaturas digitais com validade visual em documentos impressos
- Gerencia pendencias (PA, PB, PC) com ciclo de vida completo
- Suporta trabalho offline com sincronizacao automatica

### 1.4 Metricas de Sucesso

- Capacidade de gerenciar digitalmente um projeto de comissionamento completo
- Auditoria e logs de cada protocolo processado
- Rastreabilidade de todas as alteracoes (quem, quando, IP)
- Reducao de erros e retrabalho comparado ao processo manual

---

## 2. Visao e Objetivos do Projeto

### 2.1 Visao do Produto

Ser a plataforma de referencia para gerenciamento digital de protocolos de comissionamento, garantindo rastreabilidade total, conformidade e eficiencia operacional em projetos industriais complexos.

### 2.2 Objetivos Estrategicos

| Objetivo | Descricao | Indicador |
|----------|-----------|-----------|
| Digitalizacao Completa | Eliminar uso de papel e planilhas no processo de protocolos | 100% dos protocolos gerenciados digitalmente |
| Rastreabilidade Total | Manter historico completo de todas as acoes | Log de auditoria para cada operacao |
| Eficiencia Operacional | Reduzir tempo de processamento de protocolos | Medicao de tempo medio por protocolo |
| Conformidade | Garantir que todos os protocolos sigam o fluxo definido | Taxa de conformidade do fluxo |
| Mobilidade | Permitir execucao em campo via dispositivos moveis | Suporte offline funcional |

### 2.3 Escopo do Projeto

**Incluido:**
- Gerenciamento completo de protocolos C1, C2, C3 e C4
- Fluxo multi-empresa (Montadora, Gerenciadora, Comissionadora)
- Aplicacao web responsiva (PWA) para desktop e tablets
- Sistema de pendencias com tres niveis de severidade
- Assinaturas digitais
- Exportacao PDF
- Importacao de dados via Excel/CSV
- Trabalho offline com sincronizacao

**Excluido (inicialmente):**
- Integracoes com sistemas externos (ERP, SSO)
- Aplicativo nativo mobile
- Arquivamento em cold storage
- Integracao com IA

---

## 3. Personas e Papeis de Usuario

### 3.1 Tipos de Empresa

O sistema envolve tres tipos de empresa que participam do fluxo de protocolos:

| Tipo | Nome | Funcao |
|------|------|--------|
| Montadora | Assembler | Executa instalacao e preenche protocolos C1 inicialmente |
| Gerenciadora | Management Company | Revisa e aprova trabalho da Montadora |
| Comissionadora | Commissioning Company (EGT) | Autoridade final, gerencia C2/C3/C4 diretamente |

### 3.2 Papeis de Usuario

#### 3.2.1 Administrador (Admin)
- **Empresa:** Comissionadora (EGT)
- **Responsabilidades:**
  - Criar e gerenciar todas as contas de usuario
  - Configurar projetos, areas, sistemas, equipamentos
  - Criar e gerenciar templates de protocolo
  - Visualizar todos os dados do sistema
  - Importar dados via Excel/CSV
- **Acesso:** Total a todos os modulos e dados

#### 3.2.2 Tecnico (Technician)
- **Empresa:** Montadora, Comissionadora
- **Responsabilidades:**
  - Executar protocolos em campo
  - Preencher itens de protocolo
  - Registrar evidencias (fotos)
  - Sincronizar dados do tablet
- **Acesso:**
  - Apenas protocolos com status "Criado" (C1) ou "Criado/Iniciado" (C2/C3/C4)
  - Limitado a sua disciplina (C1)
  - Usa tablet em campo

#### 3.2.3 Gerente (Manager)
- **Empresa:** Montadora, Gerenciadora, Comissionadora
- **Responsabilidades:**
  - Revisar protocolos executados
  - Aprovar ou rejeitar protocolos
  - Criar pendencias (PA, PB, PC)
  - Fechar pendencias
  - Indicar uso de fornecedor (C2/C3/C4)
- **Acesso:**
  - Protocolos conforme fluxo da sua empresa
  - Limitado a sua disciplina
  - Desktop/laptop

#### 3.2.4 Engenharia (Engineering)
- **Empresa:** Montadora
- **Responsabilidades:**
  - Validar tecnicamente protocolos aprovados
  - Criar pendencias tecnicas
- **Acesso:**
  - Protocolos aprovados pelo gerente da Montadora
  - Limitado a sua disciplina

#### 3.2.5 Fornecedor (Supplier)
- **Empresa:** Externa
- **Responsabilidades:**
  - Anexar documentos aos protocolos
  - Aprovar protocolos onde foi indicado como fornecedor
- **Acesso:**
  - Apenas protocolos onde foi designado

#### 3.2.6 Cliente (Client)
- **Empresa:** Externa
- **Responsabilidades:**
  - Revisar protocolos finalizados pela Comissionadora
  - Aprovar ou rejeitar protocolos
- **Acesso:**
  - Protocolos com status "Aprovado pelo Gerente" (C2/C3/C4)

### 3.3 Matriz de Permissoes por Papel

| Acao | Admin | Tecnico | Gerente | Engenharia | Fornecedor | Cliente |
|------|-------|---------|---------|------------|------------|---------|
| Criar usuarios | Sim | Nao | Nao | Nao | Nao | Nao |
| Gerenciar projetos | Sim | Nao | Nao | Nao | Nao | Nao |
| Criar templates | Sim | Nao | Nao | Nao | Nao | Nao |
| Importar dados | Sim | Nao | Nao | Nao | Nao | Nao |
| Preencher protocolo | Nao | Sim | Nao | Nao | Nao | Nao |
| Aprovar protocolo | Nao | Nao | Sim | Nao | Sim* | Sim* |
| Rejeitar protocolo | Nao | Nao | Sim | Nao | Nao | Sim* |
| Criar pendencia | Nao | Sim** | Sim | Sim | Nao | Nao |
| Fechar pendencia | Nao | Nao | Sim | Nao | Nao | Nao |
| Anexar documentos | Nao | Sim | Sim | Sim | Sim | Nao |
| Ver todos os dados | Sim | Nao | Nao | Nao | Nao | Nao |

*Conforme fluxo especifico
**Apenas em C2/C3/C4

---

## 4. Requisitos Funcionais

### 4.1 Modulo de Autenticacao e Autorizacao

#### RF-AUTH-001: Login de Usuario
- **Descricao:** Usuario deve poder autenticar com email e senha
- **Criterios de Aceitacao:**
  - Campo de email com validacao de formato
  - Campo de senha com minimo de 8 caracteres
  - Token JWT gerado apos autenticacao bem-sucedida
  - Token expira apos periodo configuravel
  - Registro de log: usuario, data/hora, IP, sucesso/falha

#### RF-AUTH-002: Gerenciamento de Sessao
- **Descricao:** Sistema deve gerenciar sessoes de usuario
- **Criterios de Aceitacao:**
  - Refresh token para renovacao automatica
  - Logout manual disponivel
  - Sessao expira apos inatividade
  - Multiplas sessoes permitidas (desktop + tablet)

#### RF-AUTH-003: Controle de Acesso Baseado em Papeis (RBAC)
- **Descricao:** Sistema deve restringir acesso baseado no papel do usuario
- **Criterios de Aceitacao:**
  - Papel fixo atribuido na criacao do usuario
  - Disciplina atribuida restringe visualizacao (onde aplicavel)
  - Empresa do usuario determina fluxo de protocolo visivel
  - Admin tem acesso irrestrito

### 4.2 Modulo de Administracao

#### RF-ADMIN-001: Gerenciamento de Usuarios
- **Descricao:** Admin deve poder criar e gerenciar usuarios
- **Criterios de Aceitacao:**
  - Criar usuario com: nome, email, senha, empresa, papel, disciplina
  - Editar dados do usuario
  - Desativar/reativar usuario
  - Fazer upload de imagem de assinatura do usuario
  - Listar usuarios com filtros (empresa, papel, disciplina, status)
  - Log de auditoria para todas as operacoes

#### RF-ADMIN-002: Importacao de Dados via Excel/CSV
- **Descricao:** Admin deve poder importar dados em massa
- **Criterios de Aceitacao:**
  - Upload de arquivo Excel (.xlsx) ou CSV
  - Templates de importacao para cada entidade
  - Validacao de dados antes da importacao
  - Relatorio de erros detalhado
  - Importacao suporta: Empresas, Projetos, Areas, Sistemas, Subsistemas, Equipamentos, Disciplinas
  - Log de auditoria com arquivo original e resultados

### 4.3 Modulo de Empresas

#### RF-EMP-001: CRUD de Empresas
- **Descricao:** Gerenciar cadastro de empresas
- **Criterios de Aceitacao:**
  - Criar empresa com: nome, tipo (Montadora/Gerenciadora/Comissionadora/Fornecedor/Cliente), CNPJ, endereco, telefone, email
  - Editar dados da empresa
  - Desativar empresa (soft delete - nao excluir fisicamente)
  - Listar empresas com filtros
  - Log de auditoria

### 4.4 Modulo de Projetos

#### RF-PROJ-001: CRUD de Projetos
- **Descricao:** Gerenciar projetos de comissionamento
- **Criterios de Aceitacao:**
  - Criar projeto com: nome, codigo, descricao, empresa cliente, data inicio, data prevista fim
  - Associar empresas participantes (Montadora, Gerenciadora)
  - Editar dados do projeto
  - Arquivar projeto (soft delete - nao excluir fisicamente)
  - Listar projetos com filtros
  - Log de auditoria

### 4.5 Modulo de Areas

#### RF-AREA-001: CRUD de Areas
- **Descricao:** Gerenciar areas dentro de um projeto
- **Criterios de Aceitacao:**
  - Criar area com: projeto_id, nome, codigo, descricao
  - Editar area
  - Desativar area (soft delete)
  - Listar areas por projeto
  - Log de auditoria

### 4.6 Modulo de Sistemas

#### RF-SIS-001: CRUD de Sistemas
- **Descricao:** Gerenciar sistemas dentro de areas
- **Criterios de Aceitacao:**
  - Criar sistema com: area_id, nome, codigo, descricao
  - Editar sistema
  - Desativar sistema (soft delete)
  - Listar sistemas por area
  - Log de auditoria

### 4.7 Modulo de Subsistemas

#### RF-SUB-001: CRUD de Subsistemas
- **Descricao:** Gerenciar subsistemas dentro de sistemas
- **Criterios de Aceitacao:**
  - Criar subsistema com: sistema_id, nome, codigo, descricao
  - Editar subsistema
  - Desativar subsistema (soft delete)
  - Listar subsistemas por sistema
  - Verificar dependencias C2/C3/C4 baseadas em subsistema
  - Log de auditoria

### 4.8 Modulo de Disciplinas

#### RF-DISC-001: CRUD de Disciplinas
- **Descricao:** Gerenciar disciplinas tecnicas
- **Criterios de Aceitacao:**
  - Criar disciplina com: nome, codigo (ex: ELE, MEC, INS), legenda (abreviacao para exibicao)
  - Editar disciplina
  - Desativar disciplina (soft delete)
  - Listar disciplinas
  - Log de auditoria

### 4.9 Modulo de Equipamentos

#### RF-EQUIP-001: CRUD de Equipamentos
- **Descricao:** Gerenciar equipamentos
- **Criterios de Aceitacao:**
  - Criar equipamento com: subsistema_id, disciplina_id, tag_id (identificador unico), application (descricao da aplicacao)
  - Editar equipamento
  - Desativar equipamento (soft delete)
  - Listar equipamentos com filtros (subsistema, disciplina, protocol_id)
  - Associar protocolos ao equipamento
  - Log de auditoria

### 4.10 Modulo de Templates de Protocolo

#### RF-TPL-001: CRUD de Templates
- **Descricao:** Gerenciar templates de protocolo
- **Criterios de Aceitacao:**
  - Criar template com: tipo (C1/C2/C3/C4), disciplina_id (obrigatorio para C1), codigo, descricao
  - Templates sao globais (compartilhados entre projetos)
  - Editar template (apenas se nenhum protocolo derivado)
  - Versionar template quando houver protocolos derivados
  - Desativar template (soft delete)
  - Listar templates com filtros
  - Log de auditoria

#### RF-TPL-002: Gerenciamento de Itens do Template
- **Descricao:** Gerenciar itens que compoem um template
- **Criterios de Aceitacao:**
  - Adicionar item com: codigo, descricao, tipo_item, ordem, configuracao, obrigatorio, texto_ajuda
  - Tipos de item suportados:
    - `CHECKBOX_OK_NOK_NA`: 3 opcoes radio (OK, NOK, N/A)
    - `TEXT_SINGLE`: Input texto linha unica
    - `TEXT_MULTI`: Textarea multilinha
    - `NUMBER`: Input numerico com unidade opcional
    - `NUMBER_RANGE`: Dois inputs numericos (ex: Media/Alta) - campos com titulos personalizados
    - `PHOTO_CAPTURE`: Captura de foto
    - `DATE`: Seletor de data
    - `SIGNATURE`: Captura de assinatura
  - **Itens de texto (TEXT_SINGLE, TEXT_MULTI, NUMBER, NUMBER_RANGE) devem ter botao para adicionar pendencia diretamente no campo**
  - Reordenar itens via drag-and-drop
  - Editar item
  - Remover item (soft delete)
  - Log de auditoria

### 4.11 Modulo de Protocolos

#### RF-PROT-001: Criacao de Protocolos
- **Descricao:** Criar instancias de protocolo a partir de templates
- **Criterios de Aceitacao:**
  - Criar protocolo associando: template_id
  - **Protocolo pode ser relacionado a multiplos equipamentos (tags) e empresas** atraves de tabela intermediaria (ProtocolEquipmentCompany)
  - Importacao de planilha com dados de tags e vinculo com protocolo e empresa
  - Tela de edicao de relacionamento de protocolos filtrada por: empresa, disciplina, tipo de protocolo (C1, C2, C3, C4), subsistema, sistema
  - Protocolo herda todos os itens do template
  - Status inicial: "Criado"
  - Protocolos C1 requerem disciplina (herdada do template)
  - Protocolos C2/C3/C4 podem ter itens customizados na importacao
  - Log de auditoria

#### RF-PROT-001B: Tela de Edicao de Protocolos
- **Descricao:** Interface dedicada para editar relacionamentos de protocolos
- **Criterios de Aceitacao:**
  - Tela separada para editar um protocolo e seus relacionamentos
  - Filtros disponíveis: empresa, disciplina, tipo de protocolo, subsistema, sistema
  - Permitir adicionar/remover equipamentos e empresas vinculados
  - Log de auditoria

#### RF-PROT-002: Preenchimento de Protocolo (Tecnico)
- **Descricao:** Tecnico preenche itens do protocolo em campo
- **Criterios de Aceitacao:**
  - Visualizar apenas protocolos com status apropriado e da sua disciplina
  - Preencher cada item conforme seu tipo
  - Salvar parcialmente (rascunho) - status "Iniciado" para C2/C3/C4
  - Anexar fotos aos itens
  - Validacao: ao menos uma opcao selecionada para CHECKBOX_OK_NOK_NA
  - Submeter protocolo completo - muda status conforme fluxo
  - Funciona offline com sincronizacao posterior
  - Log de auditoria (cada salvamento)

#### RF-PROT-003: Revisao e Aprovacao de Protocolo
- **Descricao:** Gerente/Engenharia revisa e aprova/rejeita protocolo
- **Criterios de Aceitacao:**
  - Visualizar protocolos conforme status e papel
  - Revisar cada item preenchido
  - Aprovar item individualmente ou em lote
  - Rejeitar protocolo (limpa dados, retorna status)
  - Criar pendencias (PA/PB/PC) em itens especificos
  - Indicar fornecedor quando aplicavel (C2/C3/C4)
  - Aprovar protocolo (muda status conforme fluxo)
  - Log de auditoria

#### RF-PROT-004: Assinatura Digital de Protocolo
- **Descricao:** Registrar assinatura digital no protocolo
- **Criterios de Aceitacao:**
  - Ao aprovar/finalizar, anexar imagem de assinatura do usuario
  - Registrar data/hora e IP da assinatura
  - Assinatura visivel na exportacao PDF
  - Multiplas assinaturas por protocolo (cada aprovador)
  - Log de auditoria

#### RF-PROT-005: Exportacao PDF de Protocolo
- **Descricao:** Gerar PDF do protocolo para impressao
- **Criterios de Aceitacao:**
  - Layout formatado com cabecalho do projeto/empresa
  - Todos os itens com valores preenchidos
  - Fotos anexadas incluidas
  - Assinaturas digitais renderizadas como se fossem fisicas
  - Historico de status/aprovacoes
  - Pendencias listadas (se houver)
  - Data de geracao e usuario que gerou

### 4.12 Modulo de Pendencias

#### RF-PEND-001: Criacao de Pendencia
- **Descricao:** Registrar pendencias em itens de protocolo
- **Criterios de Aceitacao:**
  - Criar pendencia com: protocolo_item_id, tipo (PA/PB/PC), descricao
  - PA = Alta prioridade (bloqueia progressao)
  - PB = Baixa prioridade
  - PC = Sugestao de melhoria
  - Anexar fotos/documentos a pendencia
  - Status inicial: Aberta
  - Log de auditoria (criador, data/hora, IP)

#### RF-PEND-002: Gerenciamento de Pendencia
- **Descricao:** Gerenciar ciclo de vida da pendencia
- **Criterios de Aceitacao:**
  - Alterar nivel (ex: PB para PA)
  - Adicionar comentarios
  - Anexar documentos adicionais
  - Nao permitir exclusao
  - Log de auditoria para cada alteracao

#### RF-PEND-003: Resolucao de Pendencia
- **Descricao:** Marcar pendencia como resolvida
- **Criterios de Aceitacao:**
  - Marcar como resolvida (baixada)
  - Campos opcionais: descricao da resolucao, fotos, documentos
  - Registrar quem resolveu, quando, IP
  - Pendencia resolvida nao pode ser reaberta
  - Status do protocolo atualizado conforme pendencias restantes
  - Log de auditoria

### 4.13 Modulo de Status de Protocolo

#### RF-STAT-001: Historico de Status
- **Descricao:** Manter historico completo de mudancas de status
- **Criterios de Aceitacao:**
  - Registrar cada mudanca: status_anterior, status_novo, usuario, data/hora, IP, motivo
  - Historico imutavel (append-only)
  - Visualizacao de timeline do protocolo
  - Filtros por periodo, usuario, status

### 4.14 Modulo de Anexos

#### RF-ANEX-001: Upload de Arquivos
- **Descricao:** Permitir upload de fotos e PDFs
- **Criterios de Aceitacao:**
  - Formatos aceitos: JPG, PNG, PDF
  - Tamanho maximo por arquivo: 10MB (configuravel)
  - Compressao automatica de imagens grandes
  - Armazenamento em cloud storage
  - Vinculacao a: protocolo, item de protocolo, pendencia
  - Log de auditoria

#### RF-ANEX-002: Visualizacao de Anexos
- **Descricao:** Visualizar anexos inline
- **Criterios de Aceitacao:**
  - Preview de imagens
  - Visualizador PDF integrado
  - Download de arquivo original
  - Galeria de anexos por entidade

### 4.15 Modulo de Auditoria

#### RF-AUD-001: Log de Auditoria
- **Descricao:** Registrar todas as operacoes do sistema
- **Criterios de Aceitacao:**
  - Cada operacao registra: usuario_id, data/hora, endereco_ip, acao, entidade, entidade_id, dados_anteriores, dados_novos
  - Logs imutaveis (nao podem ser editados ou excluidos)
  - Consulta de logs com filtros (usuario, periodo, entidade, acao)
  - Exportacao de logs para auditoria externa

### 4.16 Modulo Offline/Sincronizacao

#### RF-SYNC-001: Modo Offline
- **Descricao:** Permitir uso do sistema sem conexao
- **Criterios de Aceitacao:**
  - PWA instalavel no tablet
  - Cache local de protocolos atribuidos ao usuario
  - Preenchimento de protocolos offline
  - Captura de fotos offline
  - Indicador visual de modo offline

#### RF-SYNC-002: Sincronizacao de Dados
- **Descricao:** Sincronizar dados quando conexao disponivel
- **Criterios de Aceitacao:**
  - Deteccao automatica de conexao
  - Sincronizacao automatica ao reconectar
  - Sincronizacao manual disponivel
  - Fila de operacoes pendentes visivel
  - Progresso de sincronizacao visivel

#### RF-SYNC-003: Resolucao de Conflitos
- **Descricao:** Tratar conflitos de sincronizacao
- **Criterios de Aceitacao:**
  - Detectar conflitos (mesmo registro alterado em dois dispositivos)
  - Estrategias de resolucao:
    - Last-write-wins para campos simples com timestamp
    - Merge automatico para campos independentes
    - Conflito manual para alteracoes incompativeis
  - Interface para resolver conflitos manualmente
  - Log de conflitos e resolucoes
  - Notificacao ao usuario sobre conflitos

---

## 5. Maquinas de Estado do Ciclo de Vida dos Protocolos

### 5.1 Fluxo C1 (Protocolos de Montagem)

```
                                    MONTADORA
    +------------------------------------------------------------------+
    |                                                                  |
    |   [CRIADO] -----(Tecnico preenche)----> [EXECUTADO_MONTADORA]    |
    |       ^                                         |                |
    |       |                                         v                |
    |       +----(Gerente rejeita)---- [REJEITADO_GERENCIADORA] <--+   |
    |       |                                         |            |   |
    |       |                          (Gerente aprova)            |   |
    |       |                                         v            |   |
    |       |                            [APROVADO_MONTADORA]      |   |
    |       |                                   |                  |   |
    |       |                      (Engenharia valida)             |   |
    |       |                            |     |     |             |   |
    |       |                            v     v     v             |   |
    |       |            [VALIDADO_ENGENHARIA_PA/PB/PC]            |   |
    |       |                                                      |   |
    +------------------------------------------------------------------+
                                         |
                                         v
                                   GERENCIADORA
    +------------------------------------------------------------------+
    |                                                                  |
    |   [VALIDADO_ENGENHARIA_*] -----(Gerente revisa)                  |
    |            |                           |                         |
    |            |              (rejeita)    |    (aprova)             |
    |            |                  |        |        |                |
    |            v                  v        v        v                |
    |   [REJEITADO_GERENCIADORA]        [APROVADO_GERENCIADORA_*]      |
    |            |                              |                      |
    |            +-----(volta para Montadora)   |                      |
    |                                           |                      |
    +------------------------------------------------------------------+
                                                |
                                                v
                                         COMISSIONADORA
    +------------------------------------------------------------------+
    |                                                                  |
    |   [APROVADO_GERENCIADORA_*] -----(Gerente revisa)                |
    |            |                           |                         |
    |            |              (rejeita)    |    (aprova)             |
    |            |                  |        |        |                |
    |            v                  v        v        v                |
    |   [REJEITADO_COMISSIONADORA]        [FINALIZADO_*]               |
    |            |                              |                      |
    |            +---(volta Gerenciadora)       |                      |
    |                                           v                      |
    |                              [FINALIZADO] (sem pendencias)       |
    |                              [FINALIZADO_PA/PB/PC] (com pend.)   |
    |                                                                  |
    +------------------------------------------------------------------+
```

### 5.2 Estados C1 Detalhados

| Estado | Codigo | Descricao | Proximo Papel |
|--------|--------|-----------|---------------|
| Criado | `CREATED` | Protocolo criado, aguardando execucao | Tecnico Montadora |
| Executado Montadora | `EXECUTED_ASSEMBLER` | Tecnico preencheu, aguarda revisao | Gerente Montadora |
| Aprovado Montadora | `APPROVED_ASSEMBLER` | Gerente aprovou, aguarda engenharia | Engenharia Montadora |
| Validado Engenharia PA | `VALIDATED_ENGINEERING_PA` | Validado com pendencia alta | Gerente Gerenciadora |
| Validado Engenharia PB | `VALIDATED_ENGINEERING_PB` | Validado com pendencia baixa | Gerente Gerenciadora |
| Validado Engenharia PC | `VALIDATED_ENGINEERING_PC` | Validado com sugestao | Gerente Gerenciadora |
| Rejeitado Gerenciadora | `REJECTED_MANAGEMENT` | Gerenciadora rejeitou | Engenharia Montadora |
| Aprovado Gerenciadora PA | `APPROVED_MANAGEMENT_PA` | Aprovado com pend. alta | Gerente Comissionadora |
| Aprovado Gerenciadora PB | `APPROVED_MANAGEMENT_PB` | Aprovado com pend. baixa | Gerente Comissionadora |
| Aprovado Gerenciadora PC | `APPROVED_MANAGEMENT_PC` | Aprovado com sugestao | Gerente Comissionadora |
| Rejeitado Comissionadora | `REJECTED_COMMISSIONING` | Comissionadora rejeitou | Gerente Gerenciadora |
| Finalizado | `FINALIZED` | Concluido sem pendencias | - |
| Finalizado PA | `FINALIZED_PA` | Concluido com pend. alta aberta | - |
| Finalizado PB | `FINALIZED_PB` | Concluido com pend. baixa aberta | - |
| Finalizado PC | `FINALIZED_PC` | Concluido com sugestao aberta | - |

### 5.3 Fluxo C2/C3/C4 (Protocolos de Comissionamento)

```
                                   COMISSIONADORA
    +------------------------------------------------------------------+
    |                                                                  |
    |   [CRIADO] -----(Tecnico inicia)----> [INICIADO]                 |
    |       ^                                    |                     |
    |       |                     (Tecnico completa)                   |
    |       |                                    v                     |
    |       |                              [EXECUTADO]                 |
    |       |                                    |                     |
    |       |                       (Gerente revisa)                   |
    |       |                          |         |                     |
    |       |             (rejeita)    |         |    (aprova)         |
    |       |                 |        |         |        |            |
    |       +-----------------+        v         v        v            |
    |       (limpa dados e             [APROVADO_GERENTE_*]            |
    |        pendencias)                         |                     |
    |                                            | (indica fornecedor  |
    |                                            |  se aplicavel)      |
    +------------------------------------------------------------------+
                                                 |
                          +----------------------+----------------------+
                          |                                             |
                          v                                             v
                    FORNECEDOR                                      CLIENTE
    +---------------------------+                   +---------------------------+
    |                           |                   |                           |
    | [APROVADO_GERENTE_*]      |                   | [APROVADO_GERENTE_*]      |
    |         |                 |                   |         |                 |
    |   (anexa docs,            |                   |   (revisa)                |
    |    aprova)                |                   |      |         |          |
    |         |                 |                   |      v         v          |
    |         v                 |                   | (rejeita)  (aprova)       |
    | [APROVADO_FORNECEDOR]     |                   |      |         |          |
    |         |                 |                   |      v         v          |
    +---------------------------+                   | [REJEITADO  [FINALIZADO_*]|
                          |                         |  _CLIENTE]                |
                          |                         |      |                    |
                          +------------------------>+      v                    |
                                                    | (volta p/                 |
                                                    |  Gerente)                 |
                                                    +---------------------------+
```

### 5.4 Estados C2/C3/C4 Detalhados

| Estado | Codigo | Descricao | Proximo Papel |
|--------|--------|-----------|---------------|
| Criado | `CREATED` | Protocolo criado | Tecnico Comissionadora |
| Iniciado | `STARTED` | Preenchimento parcial | Tecnico Comissionadora |
| Executado | `EXECUTED` | Tecnico completou | Gerente Comissionadora |
| Aprovado Gerente | `APPROVED_MANAGER` | Sem pendencias | Cliente |
| Aprovado Gerente PA | `APPROVED_MANAGER_PA` | Com pendencia alta | Cliente |
| Aprovado Gerente PB | `APPROVED_MANAGER_PB` | Com pendencia baixa | Cliente |
| Aprovado Gerente PC | `APPROVED_MANAGER_PC` | Com sugestao | Cliente |
| Rejeitado Cliente | `REJECTED_CLIENT` | Cliente rejeitou | Gerente Comissionadora |
| Finalizado | `FINALIZED` | Concluido sem pendencias | - |
| Finalizado PA | `FINALIZED_PA` | Concluido com pend. alta | - |
| Finalizado PB | `FINALIZED_PB` | Concluido com pend. baixa | - |
| Finalizado PC | `FINALIZED_PC` | Concluido com sugestao | - |

### 5.5 Dependencias entre Protocolos

| Tipo | Pre-requisito |
|------|---------------|
| C2 | Pode ser criado a qualquer momento |
| C3 | Todos os C2 do subsistema devem estar sem pendencias PA |
| C4 | Todos os C3 do subsistema devem estar sem pendencias PA |

---

## 6. Recomendacoes de Modelo de Dados

### 6.1 Diagrama Entidade-Relacionamento (Simplificado)

```
+-------------+       +-------------+       +-------------+
|   Company   |       |   Project   |       |    Area     |
+-------------+       +-------------+       +-------------+
| id          |       | id          |       | id          |
| name        |<----->| name        |<----->| project_id  |
| type        |       | code        |       | name        |
| cnpj        |       | description |       | code        |
| phone       |       | client_id   |       | ...         |
| email       |       +-------------+       +-------------+
| is_active   |                                    |
+-------------+                                    v
                      +-------------+       +-------------+
+-------------+       |   System    |       |  Subsystem  |
| Discipline  |       +-------------+       +-------------+
+-------------+       | id          |       | id          |
| id          |       | area_id     |<----->| system_id   |
| name        |       | name        |       | name        |
| code        |       | code        |       | code        |
| legend      |       +-------------+       +-------------+
| is_active   |                                    |
+-------------+                                    v
      |               +-------------+       +-------------+
      +-------------->|  Equipment  |<----->| Prot.Equip. |
                      +-------------+       |   Company   |
                      | id          |       +-------------+
                      | subsystem_id|       | id          |
                      | discipline_id       | protocol_id |<--+
                      | tag_id      |       | equipment_id|   |
                      | application |       | company_id  |   |
                      | is_active   |       +-------------+   |
                      +-------------+              |          |
                                                   v          |
                      +-------------+       +-------------+   |
                      | Protocol    |------>| Prot.Status |   |
                      +-------------+       +-------------+   |
                      | id          |-------| id          |   |
                      | template_id |       | protocol_id |   |
                      | status      |       | status      |   |
                      | is_active   |       | user_id     |   |
                      +-------------+       | timestamp   |   |
                             |              | ip_address  |   |
                             |              +-------------+   |
                             +--------------------------------+
                             |
                             v
+-------------+       +-------------+       +-------------+
| Prot.Item   |<----->|  Pendency   |       |  Attachment |
+-------------+       +-------------+       +-------------+
| id          |       | id          |       | id          |
| protocol_id |       | prot_item_id|       | entity_type |
| template_item_id    | type (PA/PB/PC)     | entity_id   |
| value       |       | description |       | file_path   |
| ...         |       | resolved    |       | file_type   |
+-------------+       | ...         |       | ...         |
                      +-------------+       +-------------+
```

**Nota Importante:** O relacionamento Protocol <-> Equipment/Company e N:N (muitos para muitos).
Um protocolo pode estar vinculado a multiplos equipamentos e empresas atraves da tabela `ProtocolEquipmentCompany`.

### 6.2 Modelo de Protocol Template Item (Recomendado)

```python
class ProtocolTemplateItem(Base):
    __tablename__ = "protocol_template_items"

    id = Column(Integer, primary_key=True)
    protocol_template_id = Column(Integer, ForeignKey("protocol_templates.id"), nullable=False)

    # Identificacao
    code = Column(String(50), nullable=False)  # Codigo unico dentro do template
    description = Column(String(500), nullable=False)
    help_text = Column(String(1000), nullable=True)  # Texto de ajuda para o tecnico
    order = Column(Integer, nullable=False)

    # Tipo e Configuracao
    item_type = Column(
        Enum(
            'CHECKBOX_OK_NOK_NA',
            'TEXT_SINGLE',
            'TEXT_MULTI',
            'NUMBER',
            'NUMBER_RANGE',
            'PHOTO_CAPTURE',
            'DATE',
            'SIGNATURE',
            name='item_type_enum'
        ),
        nullable=False
    )

    # Configuracao especifica do tipo (JSON)
    config = Column(JSON, nullable=True)
    # Exemplos de config por tipo:
    # NUMBER: {"unit": "V", "min": 0, "max": 500}
    # NUMBER_RANGE: {"label_1": "Media", "label_2": "Alta", "unit": "V"}
    # CHECKBOX_OK_NOK_NA: {"options": ["OK", "NOK", "N/A"]} -- opcional, usa padrao

    # Validacao
    required = Column(Boolean, default=True)

    # Auditoria
    created_at = Column(DateTime, default=datetime.utcnow)
    created_by = Column(Integer, ForeignKey("users.id"))
    updated_at = Column(DateTime, onupdate=datetime.utcnow)
    updated_by = Column(Integer, ForeignKey("users.id"))
```

### 6.3 Modelo de Protocol Item (Valores Preenchidos)

```python
class ProtocolItem(Base):
    __tablename__ = "protocol_items"

    id = Column(Integer, primary_key=True)
    protocol_id = Column(Integer, ForeignKey("protocols.id"), nullable=False)
    template_item_id = Column(Integer, ForeignKey("protocol_template_items.id"), nullable=False)

    # Valor preenchido (JSON para flexibilidade)
    value = Column(JSON, nullable=True)
    # Exemplos de value por tipo:
    # CHECKBOX_OK_NOK_NA: {"selected": "OK"}
    # TEXT_SINGLE: {"text": "Serial ABC123"}
    # NUMBER: {"number": 220.5}
    # NUMBER_RANGE: {"value_1": 110.0, "value_2": 220.0}
    # PHOTO_CAPTURE: {"attachment_ids": [1, 2, 3]}
    # DATE: {"date": "2025-12-17"}
    # SIGNATURE: {"signature_id": 1, "signed_at": "2025-12-17T10:30:00Z"}

    # Status de aprovacao do item
    approval_status = Column(
        Enum('PENDING', 'APPROVED', 'REJECTED', name='item_approval_enum'),
        default='PENDING'
    )

    # Auditoria
    filled_at = Column(DateTime)
    filled_by = Column(Integer, ForeignKey("users.id"))
    filled_ip = Column(String(45))
    approved_at = Column(DateTime)
    approved_by = Column(Integer, ForeignKey("users.id"))
    approved_ip = Column(String(45))
```

### 6.4 Modelo de Auditoria

```python
class AuditLog(Base):
    __tablename__ = "audit_logs"

    id = Column(BigInteger, primary_key=True)

    # Quem
    user_id = Column(Integer, ForeignKey("users.id"), nullable=False)

    # Quando
    timestamp = Column(DateTime, default=datetime.utcnow, nullable=False)

    # De onde
    ip_address = Column(String(45), nullable=False)
    user_agent = Column(String(500), nullable=True)

    # O que
    action = Column(
        Enum('CREATE', 'UPDATE', 'DELETE', 'LOGIN', 'LOGOUT', 'APPROVE',
             'REJECT', 'SIGN', 'SYNC', name='audit_action_enum'),
        nullable=False
    )
    entity_type = Column(String(100), nullable=False)  # ex: "Protocol", "ProtocolItem"
    entity_id = Column(Integer, nullable=False)

    # Detalhes
    previous_data = Column(JSON, nullable=True)  # Estado anterior
    new_data = Column(JSON, nullable=True)  # Novo estado
    metadata = Column(JSON, nullable=True)  # Dados adicionais (ex: motivo rejeicao)

    # Indice para consultas
    __table_args__ = (
        Index('ix_audit_entity', 'entity_type', 'entity_id'),
        Index('ix_audit_user_time', 'user_id', 'timestamp'),
    )
```

### 6.5 Modelo de Usuario com Assinatura

```python
class User(Base):
    __tablename__ = "users"

    id = Column(Integer, primary_key=True)
    email = Column(String(255), unique=True, nullable=False)
    password_hash = Column(String(255), nullable=False)

    # Dados pessoais
    name = Column(String(255), nullable=False)

    # Vinculacao
    company_id = Column(Integer, ForeignKey("companies.id"), nullable=False)
    role = Column(
        Enum('ADMIN', 'TECHNICIAN', 'MANAGER', 'ENGINEERING',
             'SUPPLIER', 'CLIENT', name='user_role_enum'),
        nullable=False
    )
    discipline_id = Column(Integer, ForeignKey("disciplines.id"), nullable=True)

    # Assinatura digital
    signature_image_path = Column(String(500), nullable=True)  # Path no storage

    # Status
    is_active = Column(Boolean, default=True)

    # Auditoria
    created_at = Column(DateTime, default=datetime.utcnow)
    updated_at = Column(DateTime, onupdate=datetime.utcnow)
```

### 6.6 Modelo de Company (Empresa)

```python
class Company(Base):
    __tablename__ = "companies"

    id = Column(Integer, primary_key=True)
    name = Column(String(255), nullable=False)
    type = Column(
        Enum('MONTADORA', 'GERENCIADORA', 'COMISSIONADORA',
             'FORNECEDOR', 'CLIENTE', name='company_type_enum'),
        nullable=False
    )
    cnpj = Column(String(18), nullable=True)
    address = Column(String(500), nullable=True)
    phone = Column(String(20), nullable=True)
    email = Column(String(255), nullable=True)

    # Soft delete
    is_active = Column(Boolean, default=True)

    # Auditoria
    created_at = Column(DateTime, default=datetime.utcnow)
    updated_at = Column(DateTime, onupdate=datetime.utcnow)
```

### 6.7 Modelo de Discipline (Disciplina)

```python
class Discipline(Base):
    __tablename__ = "disciplines"

    id = Column(Integer, primary_key=True)
    name = Column(String(100), nullable=False)
    code = Column(String(10), nullable=False)  # ex: ELE, MEC, INS
    legend = Column(String(5), nullable=False)  # Abreviacao para exibicao (ex: E, M, I)

    # Soft delete
    is_active = Column(Boolean, default=True)

    # Auditoria
    created_at = Column(DateTime, default=datetime.utcnow)
    updated_at = Column(DateTime, onupdate=datetime.utcnow)
```

### 6.8 Modelo de Equipment (Equipamento)

```python
class Equipment(Base):
    __tablename__ = "equipments"

    id = Column(Integer, primary_key=True)
    tag_id = Column(String(50), nullable=False, unique=True)  # Identificador unico do equipamento
    application = Column(String(500), nullable=False)  # Descricao da aplicacao

    # Relacionamentos
    subsystem_id = Column(Integer, ForeignKey("subsystems.id"), nullable=False)
    discipline_id = Column(Integer, ForeignKey("disciplines.id"), nullable=True)

    # Soft delete
    is_active = Column(Boolean, default=True)

    # Auditoria
    created_at = Column(DateTime, default=datetime.utcnow)
    updated_at = Column(DateTime, onupdate=datetime.utcnow)
```

### 6.9 Modelo de ProtocolEquipmentCompany (Relacionamento N:N)

```python
class ProtocolEquipmentCompany(Base):
    """
    Tabela intermediaria para relacionamento N:N entre Protocol, Equipment e Company.
    Um protocolo pode ser aplicado a multiplos equipamentos por diferentes empresas.
    """
    __tablename__ = "protocol_equipment_companies"

    id = Column(Integer, primary_key=True)
    protocol_id = Column(Integer, ForeignKey("protocols.id"), nullable=False)
    equipment_id = Column(Integer, ForeignKey("equipments.id"), nullable=False)
    company_id = Column(Integer, ForeignKey("companies.id"), nullable=False)

    # Status especifico deste vinculo
    status = Column(String(50), default='CREATED')

    # Soft delete
    is_active = Column(Boolean, default=True)

    # Auditoria
    created_at = Column(DateTime, default=datetime.utcnow)
    updated_at = Column(DateTime, onupdate=datetime.utcnow)

    # Constraint para evitar duplicatas
    __table_args__ = (
        UniqueConstraint('protocol_id', 'equipment_id', 'company_id',
                         name='uq_protocol_equipment_company'),
    )
```

### 6.10 Politica de Soft Delete

**IMPORTANTE:** Todas as exclusoes no sistema sao soft delete. Nenhum registro e excluido fisicamente do banco de dados.

| Entidade | Campo | Comportamento |
|----------|-------|---------------|
| Company | `is_active` | `False` = empresa desativada |
| Project | `is_active` | `False` = projeto arquivado |
| Area | `is_active` | `False` = area desativada |
| System | `is_active` | `False` = sistema desativado |
| Subsystem | `is_active` | `False` = subsistema desativado |
| Discipline | `is_active` | `False` = disciplina desativada |
| Equipment | `is_active` | `False` = equipamento desativado |
| ProtocolTemplate | `is_active` | `False` = template desativado |
| ProtocolTemplateItem | `is_active` | `False` = item de template removido |
| Protocol | `is_active` | `False` = protocolo arquivado |
| ProtocolEquipmentCompany | `is_active` | `False` = vinculo removido |
| User | `is_active` | `False` = usuario desativado |
| Pendency | N/A | **Pendencias NAO podem ser excluidas** |
| AuditLog | N/A | **Logs NAO podem ser excluidos** |

---

## 7. Requisitos Nao-Funcionais

### 7.1 Performance

| Requisito | Especificacao |
|-----------|---------------|
| RNF-PERF-001 | Tempo de resposta da API < 500ms para 95% das requisicoes |
| RNF-PERF-002 | Listagem de protocolos com paginacao (max 100 itens/pagina) |
| RNF-PERF-003 | Upload de imagem < 5s para arquivos ate 10MB |
| RNF-PERF-004 | Geracao de PDF < 10s para protocolos com ate 1000 itens |
| RNF-PERF-005 | Sincronizacao offline < 30s para 100 protocolos |

### 7.2 Escalabilidade

| Requisito | Especificacao |
|-----------|---------------|
| RNF-SCAL-001 | Suportar 50 usuarios simultaneos |
| RNF-SCAL-002 | Suportar 100.000 protocolos por projeto |
| RNF-SCAL-003 | Suportar 1.000.000 itens de protocolo por projeto |
| RNF-SCAL-004 | Banco de dados escalavel horizontalmente |
| RNF-SCAL-005 | Storage de arquivos escalavel (cloud storage) |

### 7.3 Disponibilidade

| Requisito | Especificacao |
|-----------|---------------|
| RNF-DISP-001 | Disponibilidade 99.5% (excluindo manutencao programada) |
| RNF-DISP-002 | Backup diario automatico do banco de dados |
| RNF-DISP-003 | Recuperacao de backup em < 4 horas |
| RNF-DISP-004 | PWA funcional offline para operacoes criticas |

### 7.4 Seguranca

| Requisito | Especificacao |
|-----------|---------------|
| RNF-SEG-001 | Autenticacao via JWT com expiracao configuravel |
| RNF-SEG-002 | Senhas armazenadas com hash bcrypt (custo 12) |
| RNF-SEG-003 | HTTPS obrigatorio em producao |
| RNF-SEG-004 | CORS configurado para dominios autorizados |
| RNF-SEG-005 | Rate limiting: 100 req/min por usuario |
| RNF-SEG-006 | Validacao de entrada em todos os endpoints |
| RNF-SEG-007 | SQL injection prevention via ORM |
| RNF-SEG-008 | XSS prevention no frontend |
| RNF-SEG-009 | Segregacao de dados por empresa |

### 7.5 Auditoria e Compliance

| Requisito | Especificacao |
|-----------|---------------|
| RNF-AUD-001 | Log de todas as operacoes CRUD |
| RNF-AUD-002 | Registro de IP em todas as operacoes |
| RNF-AUD-003 | Logs imutaveis (append-only) |
| RNF-AUD-004 | Retencao de logs por 5 anos minimo |
| RNF-AUD-005 | Exportacao de logs para auditoria |

### 7.6 Usabilidade

| Requisito | Especificacao |
|-----------|---------------|
| RNF-USA-001 | Interface responsiva (desktop e tablet) |
| RNF-USA-002 | Suporte a telas de 10" ou maiores |
| RNF-USA-003 | Tempo de aprendizado < 2 horas para tecnicos |
| RNF-USA-004 | Indicadores visuais claros de status e erros |
| RNF-USA-005 | Feedback visual de operacoes em andamento |
| RNF-USA-006 | Interface em Portugues (Brasil) |

### 7.7 Compatibilidade

| Requisito | Especificacao |
|-----------|---------------|
| RNF-COMP-001 | Navegadores: Chrome 90+, Firefox 88+, Safari 14+, Edge 90+ |
| RNF-COMP-002 | PWA instalavel em Android 8+ e iOS 14+ |
| RNF-COMP-003 | Resolucao minima: 1024x768 (desktop), 768x1024 (tablet) |

---

## 8. Arquitetura Tecnica

### 8.1 Visao Geral

```
+------------------+     +------------------+     +------------------+
|                  |     |                  |     |                  |
|  Frontend (PWA)  |<--->|  Backend (API)   |<--->|    Database      |
|  React           |     |  FastAPI         |     |    PostgreSQL    |
|                  |     |  SQLAlchemy      |     |                  |
+------------------+     +------------------+     +------------------+
        |                        |
        |                        v
        |                +------------------+
        |                |                  |
        +--------------->|  Cloud Storage   |
         (uploads)       |  (S3/GCS/Azure)  |
                         |                  |
                         +------------------+
```

### 8.2 Stack Tecnologico

#### Backend
| Componente | Tecnologia | Justificativa |
|------------|------------|---------------|
| Framework | FastAPI | Async, alta performance, documentacao automatica |
| ORM | SQLAlchemy 2.0 | Maturidade, flexibilidade, migrações com Alembic |
| Banco de Dados | PostgreSQL 15+ | ACID, JSON support, performance, confiabilidade |
| Autenticacao | JWT (python-jose) | Stateless, padrao de mercado |
| Validacao | Pydantic v2 | Integrado ao FastAPI, type hints |
| Migrações | Alembic | Padrao para SQLAlchemy |
| Testes | Pytest + httpx | Cobertura de testes async |

#### Frontend
| Componente | Tecnologia | Justificativa |
|------------|------------|---------------|
| Framework | React 18+ | Ecossistema, comunidade, performance |
| State Management | Zustand ou Redux Toolkit | Simplicidade vs robustez |
| UI Components | Material-UI ou Ant Design | Componentes prontos, responsivos |
| Forms | React Hook Form | Performance, validacao |
| HTTP Client | Axios ou TanStack Query | Cache, retry, offline support |
| PWA | Workbox | Service workers, offline |
| Offline Storage | IndexedDB (Dexie.js) | Storage estruturado no browser |
| PDF Generation | React-PDF ou jsPDF | Geracao client-side |

#### Infraestrutura
| Componente | Tecnologia | Justificativa |
|------------|------------|---------------|
| Cloud Provider | AWS / GCP / Azure | Flexibilidade |
| Container | Docker | Portabilidade |
| Orquestracao | Docker Compose (dev) / K8s (prod) | Escalabilidade |
| CI/CD | GitHub Actions | Integracao com repositorio |
| Storage | S3 / GCS / Azure Blob | Arquivos e imagens |
| CDN | CloudFront / CloudFlare | Performance frontend |

### 8.3 Estrutura de Projetos

#### Backend (pws-api)
```
pws-api/
├── alembic/                    # Migrações de banco
│   └── versions/
├── app/
│   ├── api/
│   │   ├── v1/
│   │   │   ├── endpoints/
│   │   │   │   ├── auth.py
│   │   │   │   ├── users.py
│   │   │   │   ├── companies.py
│   │   │   │   ├── projects.py
│   │   │   │   ├── areas.py
│   │   │   │   ├── systems.py
│   │   │   │   ├── subsystems.py
│   │   │   │   ├── disciplines.py
│   │   │   │   ├── equipment.py
│   │   │   │   ├── protocol_templates.py
│   │   │   │   ├── protocols.py
│   │   │   │   ├── pendencies.py
│   │   │   │   └── imports.py
│   │   │   └── router.py
│   │   └── deps.py             # Dependencias (auth, db)
│   ├── core/
│   │   ├── config.py           # Configuracoes
│   │   ├── security.py         # JWT, hashing
│   │   └── audit.py            # Middleware de auditoria
│   ├── models/                 # SQLAlchemy models
│   │   ├── user.py
│   │   ├── company.py
│   │   ├── project.py
│   │   ├── protocol.py
│   │   └── ...
│   ├── schemas/                # Pydantic schemas
│   │   ├── user.py
│   │   ├── protocol.py
│   │   └── ...
│   ├── services/               # Business logic
│   │   ├── protocol_service.py
│   │   ├── import_service.py
│   │   └── pdf_service.py
│   ├── db/
│   │   ├── session.py
│   │   └── base.py
│   └── main.py
├── tests/
├── requirements.txt
├── Dockerfile
└── docker-compose.yml
```

#### Frontend (pws-web)
```
pws-web/
├── public/
│   ├── manifest.json           # PWA manifest
│   └── sw.js                   # Service worker
├── src/
│   ├── api/                    # API clients
│   │   ├── client.ts
│   │   ├── auth.ts
│   │   ├── protocols.ts
│   │   └── ...
│   ├── components/
│   │   ├── common/             # Componentes reutilizaveis
│   │   ├── layout/             # Header, Sidebar, etc
│   │   ├── forms/              # Form components
│   │   └── protocol/           # Componentes de protocolo
│   ├── pages/
│   │   ├── Login/
│   │   ├── Dashboard/
│   │   ├── Projects/
│   │   ├── Protocols/
│   │   ├── Admin/
│   │   └── ...
│   ├── hooks/                  # Custom hooks
│   │   ├── useAuth.ts
│   │   ├── useOffline.ts
│   │   └── useSync.ts
│   ├── store/                  # State management
│   │   ├── authStore.ts
│   │   ├── protocolStore.ts
│   │   └── syncStore.ts
│   ├── services/
│   │   ├── offline/            # IndexedDB, sync logic
│   │   └── pdf/                # PDF generation
│   ├── utils/
│   ├── types/                  # TypeScript types
│   └── App.tsx
├── package.json
├── vite.config.ts
├── Dockerfile
└── nginx.conf
```

### 8.4 Estrategia de Sincronizacao Offline

#### 8.4.1 Dados para Cache Local

| Entidade | Cache Local | Justificativa |
|----------|-------------|---------------|
| Usuario logado | Sim | Necessario para autenticacao offline |
| Projetos atribuidos | Sim | Contexto de trabalho |
| Protocolos atribuidos | Sim | Execucao em campo |
| Templates de itens | Sim | Renderizacao de formularios |
| Disciplinas | Sim | Filtros e validacao |
| Pendencias | Sim | Visualizacao e criacao |

#### 8.4.2 Fluxo de Sincronizacao

```
1. LOGIN (Online)
   └── Download dados do usuario
   └── Download protocolos atribuidos (status: CREATED, STARTED)
   └── Cache em IndexedDB

2. TRABALHO OFFLINE
   └── Ler dados do IndexedDB
   └── Preencher protocolos
   └── Salvar alteracoes localmente
   └── Adicionar operacoes a fila de sync

3. RECONEXAO
   └── Detectar conexao disponivel
   └── Processar fila de sync (FIFO)
   └── Para cada operacao:
       ├── Enviar para API
       ├── Se sucesso: remover da fila
       └── Se conflito: marcar para resolucao manual

4. RESOLUCAO DE CONFLITOS
   └── Comparar versao local vs servidor
   └── Se campos diferentes alterados: merge automatico
   └── Se mesmo campo alterado:
       ├── Apresentar ambas versoes ao usuario
       └── Usuario escolhe qual manter
```

#### 8.4.3 Estrutura da Fila de Sincronizacao

```typescript
interface SyncQueueItem {
  id: string;                    // UUID local
  operation: 'CREATE' | 'UPDATE' | 'DELETE';
  entity: string;                // 'protocol_item', 'pendency', etc
  entityId: number | null;       // null para CREATE
  localId: string;               // ID temporario local
  data: Record<string, any>;     // Dados da operacao
  timestamp: Date;               // Quando foi criado
  attempts: number;              // Tentativas de sync
  lastError?: string;            // Ultimo erro
  status: 'PENDING' | 'SYNCING' | 'CONFLICT' | 'FAILED';
}
```

---

## 9. Definicao do Escopo MVP

### 9.1 Criterios de Inclusao no MVP

O MVP deve permitir que a EGT execute digitalmente um projeto de comissionamento completo usando o fluxo C1. Features sao priorizadas usando MoSCoW:

- **Must Have:** Essencial para o MVP funcionar
- **Should Have:** Importante, mas MVP funciona sem
- **Could Have:** Desejavel, baixa prioridade
- **Won't Have:** Fora do escopo do MVP

### 9.2 MVP - Must Have

#### Modulo de Autenticacao
- [x] Login com email/senha
- [x] JWT com refresh token
- [x] Logout

#### Modulo de Administracao
- [x] CRUD de usuarios (Admin only)
- [x] Upload de imagem de assinatura
- [x] Importacao de dados via Excel/CSV
  - [x] Empresas
  - [x] Projetos
  - [x] Areas
  - [x] Sistemas
  - [x] Subsistemas
  - [x] Equipamentos
  - [x] Disciplinas
  - [x] Templates de protocolo

#### Modulos de Cadastro
- [x] CRUD Empresas
- [x] CRUD Projetos
- [x] CRUD Areas
- [x] CRUD Sistemas
- [x] CRUD Subsistemas
- [x] CRUD Disciplinas
- [x] CRUD Equipamentos

#### Modulo de Templates
- [x] CRUD Templates de Protocolo
- [x] CRUD Itens de Template
- [x] Tipos de item: CHECKBOX_OK_NOK_NA, TEXT_SINGLE, NUMBER

#### Modulo de Protocolos (Fluxo C1 Completo)
- [x] Criar protocolos a partir de templates
- [x] Listar protocolos por status/disciplina/empresa
- [x] Preencher protocolo (Tecnico Montadora)
- [x] Aprovar/Rejeitar (Gerente Montadora)
- [x] Validar (Engenharia Montadora)
- [x] Aprovar/Rejeitar (Gerente Gerenciadora)
- [x] Aprovar/Rejeitar/Finalizar (Gerente Comissionadora)
- [x] Transicoes de status conforme maquina de estados

#### Modulo de Pendencias
- [x] Criar pendencia (PA/PB/PC)
- [x] Listar pendencias por protocolo
- [x] Alterar nivel de pendencia
- [x] Resolver pendencia
- [x] Status do protocolo reflete pendencias abertas

#### Modulo de Assinatura
- [x] Anexar assinatura ao aprovar protocolo
- [x] Registrar data/hora/IP da assinatura

#### Auditoria
- [x] Log de todas as operacoes
- [x] Registro de usuario, timestamp, IP

#### Frontend
- [x] Telas de login
- [x] Dashboard basico
- [x] Telas de CRUD para todas as entidades
- [x] Tela de preenchimento de protocolo
- [x] Tela de revisao/aprovacao
- [x] Lista de pendencias
- [x] Responsivo para desktop e tablet

### 9.3 MVP - Should Have (Pos-MVP imediato)

- [ ] Exportacao PDF de protocolo
- [ ] Modo offline basico (visualizacao)
- [ ] Anexos de fotos em itens
- [ ] Filtros avancados em listagens

### 9.4 MVP - Could Have (Fase 2)

- [ ] Fluxo C2/C3/C4 completo
- [ ] Sincronizacao offline completa
- [ ] Resolucao de conflitos
- [ ] Dashboard com metricas
- [ ] Tipos de item adicionais (NUMBER_RANGE, PHOTO_CAPTURE, DATE, SIGNATURE)

### 9.5 MVP - Won't Have (Futuro)

- [ ] Integracoes externas
- [ ] Aplicativo nativo mobile
- [ ] Arquivamento em cold storage
- [ ] Integracao com IA
- [ ] Multi-idioma

### 9.6 Estimativa de Esforco MVP

| Modulo | Backend | Frontend | Total |
|--------|---------|----------|-------|
| Autenticacao | 3 dias | 2 dias | 5 dias |
| Administracao (CRUD usuarios) | 2 dias | 3 dias | 5 dias |
| Importacao Excel/CSV | 5 dias | 2 dias | 7 dias |
| Cadastros (7 entidades) | 7 dias | 10 dias | 17 dias |
| Templates | 3 dias | 4 dias | 7 dias |
| Protocolos + Status | 8 dias | 10 dias | 18 dias |
| Pendencias | 3 dias | 4 dias | 7 dias |
| Assinaturas | 2 dias | 2 dias | 4 dias |
| Auditoria | 3 dias | 1 dia | 4 dias |
| Infraestrutura/DevOps | 5 dias | 3 dias | 8 dias |
| Testes e QA | 5 dias | 5 dias | 10 dias |
| **TOTAL** | **46 dias** | **46 dias** | **~92 dias** |

*Estimativa considerando 1 desenvolvedor backend + 1 desenvolvedor frontend trabalhando em paralelo: ~3-4 meses*

---

## 10. Fases Futuras

### 10.1 Fase 2: Fluxo Completo e Offline

**Duracao Estimada:** 2-3 meses

**Escopo:**
- Fluxo C2/C3/C4 completo
- Modo offline com sincronizacao
- Resolucao de conflitos
- Exportacao PDF
- Tipos de item adicionais
- Dashboard com metricas basicas

### 10.2 Fase 3: Aprimoramentos

**Duracao Estimada:** 1-2 meses

**Escopo:**
- Dashboard avancado com graficos
- Relatorios de produtividade
- Notificacoes (email, push)
- Melhorias de UX baseadas em feedback
- Performance optimization

### 10.3 Fase 4: Expansao

**Duracao Estimada:** A definir

**Escopo:**
- Integracoes externas (se necessario)
- Aplicativo nativo (se PWA insuficiente)
- Arquivamento em cold storage
- Features baseadas em feedback de uso real

---

## 11. Riscos e Mitigacoes

### 11.1 Riscos Tecnicos

| Risco | Probabilidade | Impacto | Mitigacao |
|-------|---------------|---------|-----------|
| Complexidade da sincronizacao offline | Alta | Alto | Implementar em fases; comecar com sync simples; usar bibliotecas maduras (Workbox, Dexie) |
| Performance com milhares de itens por protocolo | Media | Alto | Paginacao; lazy loading; virtualizacao de listas; indices no banco |
| Conflitos de sincronizacao | Media | Medio | Estrategia clara de resolucao; UI intuitiva para conflitos; logs detalhados |
| Geracao de PDF complexa | Media | Medio | Usar biblioteca robusta; testar com protocolos grandes; fallback para geracao server-side |

### 11.2 Riscos de Projeto

| Risco | Probabilidade | Impacto | Mitigacao |
|-------|---------------|---------|-----------|
| Requisitos mal compreendidos | Media | Alto | PRD detalhado; validacao com stakeholder antes do desenvolvimento; entregas incrementais |
| Escopo crescente (scope creep) | Alta | Alto | MVP bem definido; backlog priorizado; controle de mudancas |
| Bugs em producao | Media | Alto | Testes automatizados; QA rigoroso; ambiente de staging; monitoramento |
| Dificuldade no frontend | Alta* | Medio | Usar componentes prontos; templates de UI; documentacao clara; possivel consultoria |

*Conforme mencionado pelo stakeholder

### 11.3 Riscos de Adocao

| Risco | Probabilidade | Impacto | Mitigacao |
|-------|---------------|---------|-----------|
| Resistencia dos tecnicos em campo | Media | Alto | UI intuitiva; treinamento; suporte durante transicao; feedback loop |
| Problemas de conectividade em campo | Alta | Alto | Modo offline robusto; indicadores claros de status; sync automatico |
| Curva de aprendizado | Media | Medio | Documentacao; videos tutoriais; suporte inicial intensivo |

### 11.4 Plano de Contingencia

1. **Se offline sync for muito complexo:** Lancar MVP sem offline, adicionar posteriormente
2. **Se frontend atrasar:** Considerar contratar freelancer React experiente
3. **Se performance degradar:** Implementar cache agressivo; considerar paginacao server-side
4. **Se adocao for baixa:** Conduzir sessoes de feedback; ajustar UX; oferecer treinamento adicional

---

## 12. Apendice

### 12.1 Glossario

| Termo | Definicao |
|-------|-----------|
| C1 | Protocolo de montagem/instalacao, envolve Montadora, Gerenciadora e Comissionadora |
| C2 | Protocolo de comissionamento nivel 2 |
| C3 | Protocolo de comissionamento nivel 3, depende de C2 |
| C4 | Protocolo de comissionamento nivel 4, depende de C3 |
| Comissionadora | Empresa responsavel pelo comissionamento (EGT) |
| Disciplina | Categoria tecnica (Eletrica, Mecanica, Instrumentacao, etc) |
| Gerenciadora | Empresa que gerencia e supervisiona o projeto |
| Montadora | Empresa que executa a montagem/instalacao |
| PA | Pendencia tipo A - Alta prioridade, bloqueia progressao |
| PB | Pendencia tipo B - Baixa prioridade |
| PC | Pendencia tipo C - Sugestao de melhoria |
| Pendencia | Issue/problema identificado em um item de protocolo |
| Protocolo | Instancia de checklist aplicada a um equipamento |
| PWA | Progressive Web App - aplicacao web instalavel |
| Template | Modelo de protocolo que define os itens a serem preenchidos |

### 12.2 Referencias

- FastAPI Documentation: https://fastapi.tiangolo.com/
- SQLAlchemy 2.0 Documentation: https://docs.sqlalchemy.org/
- React Documentation: https://react.dev/
- PWA Documentation: https://web.dev/progressive-web-apps/
- Workbox (Service Workers): https://developer.chrome.com/docs/workbox/
- Dexie.js (IndexedDB): https://dexie.org/

### 12.3 Historico de Revisoes

| Versao | Data | Autor | Descricao |
|--------|------|-------|-----------|
| 1.0 | 17/12/2025 | Discovery Session | Versao inicial do PRD |
| 1.1 | 17/12/2025 | Revisao Comparativa | Alinhamento com documento tecnico original: Company (phone, email), Equipment (tag_id, application), Discipline (legend), Protocol N:N Equipment/Company, soft delete global, botao pendencia em campos texto |

---

**Fim do Documento**

*Este PRD foi gerado atraves de uma sessao de descoberta estruturada e revisado para alinhamento com documentacao tecnica existente.*
