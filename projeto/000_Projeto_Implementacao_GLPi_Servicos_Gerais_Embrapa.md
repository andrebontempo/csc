# Projeto de Implementação do GLPi — Central de Serviços Compartilhados (CSC) / Serviços Gerais da Embrapa

| Campo | Valor |
|-------|-------|
| **Versão** | 1.0 |
| **Data** | Agosto de 2026 |
| **Classificação** | Uso Interno |
| **Organização** | Embrapa — Empresa Brasileira de Pesquisa Agropecuária |
| **Plataforma** | GLPi v11 (Open Source ITSM / ESM) |
| **Escopo Inicial** | Gestão de Chamados de Serviços Gerais (SEDE + 43 Unidades Descentralizadas) |

---

### Histórico de Revisões

| Versão | Data | Autor | Descrição |
|--------|------|-----------|-----------|
| 1.0 | Agosto/2026 | André Luiz Bontempo | Elaboração do projeto arquitetural de implementação do GLPi para Serviços Gerais (44 Entidades), incluindo análise de governança, licenciamento, catálogo inicial e mapa de expansão corporativa. |

---

## Sumário

1. [Sumário Executivo](#sumário-executivo)
2. [Capítulo 1 — Introdução, Visão Geral e Objetivos](#capítulo-1--introdução-visão-geral-e-objetivos)
3. [Capítulo 2 — Princípios Corporativos e Governança de Licenciamento](#capítulo-2--princípios-corporativos-e-governança-de-licenciamento)
4. [Capítulo 3 — Arquitetura Multientidades (44 Entidades) e Gestão de Acessos](#capítulo-3--arquitetura-multientidades-44-entidades-e-gestão-de-acessos)
5. [Capítulo 4 — Catálogo de Serviços Gerais e Desenho de Processos](#capítulo-4--catálogo-de-serviços-gerais-e-desenho-de-processos)
6. [Capítulo 5 — Integrações Técnicas e Ecossistema Corporativo](#capítulo-5--integrações-técnicas-e-ecossistema-corporativo)
7. [Capítulo 6 — Governança Operacional, SLAs e Indicadores (KPIs)](#capítulo-6--governança-operacional-slas-e-indicadores-kpis)
8. [Capítulo 7 — Integração com a Gestão do Conhecimento (KCS / BookStack)](#capítulo-7--integração-com-a-gestão-do-conhecimento-kcs--bookstack)
9. [Capítulo 8 — Roadmap de Implementação e Plano de Fases](#capítulo-8--roadmap-de-implementação-e-plano-de-fases)
10. [Considerações Finais](#considerações-finais)
11. [Anexo A — Relação das 44 Entidades Corporativas da Embrapa](#anexo-a--relação-das-44-entidades-corporativas-da-embrapa)
12. [Anexo B — Matriz Técnica de Plugins e Funcionalidades GLPi](#anexo-b--matriz-técnica-de-plugins-e-funcionalidades-glpi)
13. [Anexo C — Glossário de Siglas e Termos Corporativos](#anexo-c--glossário-de-siglas-e-termos-corporativos)

---

## Sumário Executivo

A Embrapa (Empresa Brasileira de Pesquisa Agropecuária) possui uma estrutura organizacional de alta complexidade e ampla distribuição geográfica, composta pela **SEDE** (em Brasília-DF) e **43 Unidades Descentralizadas (UDs)** espalhadas por todas as regiões do Brasil. A gestão de chamados de **Serviços Gerais** (manutenção predial, segurança, limpeza, marcenaria, infraestrutura física e telefonia) opera historicamente com disparidade de processos, ausência de métricas unificadas de SLA e baixa visibilidade dos custos operacionais regionais.

Este documento estabelece o **Projeto de Implementação Corporativa do GLPi (v11)** no âmbito da **Central de Serviços Compartilhados (CSC)** da Embrapa. A solução foi desenhada para atuar como uma plataforma unificada de *Enterprise Service Management* (ESM), habilitando o atendimento multi-tenant parametrizado em **44 Entidades independentes com herança hierárquica**.

A estratégia arquitetural adota prioritariamente a versão **GLPi Community Edition**, sustentada por um modelo de **Governança Interna Enterprise** (ambientes segregados de Homologação/Produção, rotinas automatizadas de backup e sincronização, monitoramento contínuo da saúde do sistema e clusterização de alta disponibilidade). Avalia-se também a viabilidade de contratação de subscrições *GLPI Network* para cenários com demanda de suporte Nível 3 oficial do fabricante ou plugins exclusivos.

No **escopo inicial (Fase 1)**, a plataforma atenderá 4 categorias principais e 22 subcategorias da área de Serviços Gerais:
1. **Limpeza / Conservação** (Alvenaria, Chaveiro, Resíduos Sólidos, Limpeza-Higienização Predial, Pintura)
2. **Logística / Segurança** (Acesso ao Parque, Almoxarifado, Autorização para Condução, Incidente no Estacionamento, Auditórios e Espaços Comuns, Cadastro de Veículo, Agendamento de Veículo, Cadastro no SISDAP)
3. **Manutenção / Infraestrutura** (Ar Condicionado, Cabeamento Lógico, Marcenaria, Paisagismo Interno, Elevador, Hidráulica, Rede de Telefonia, Rede Elétrica)
4. **Outros** (Serviço Não Classificado)

A arquitetura foi concebida modularmente para permitir a **expansão futura sem retrabalho estrutural** para os setores de **Recursos Humanos (RH)**, **Financeiro/Suprimentos** e o **SAC Corporativo da Embrapa**. 

Integrações nativas e orquestradas via **n8n**, autenticação unificada por **AD/LDAP** e alinhamento com a **Base de Conhecimento KCS (BookStack)** completam este ecossistema moderno, eficiente e orientado a dados.

---

# Capítulo 1 — Introdução, Visão Geral e Objetivos

## 1.1 Contexto Organizacional

A Embrapa é uma instituição de referência internacional em pesquisa agropecuária. Sua presença territorial abrange centros de pesquisa temáticos, ecorregionais e de produtos, além da infraestrutura administrativa central na SEDE.

| Dimensão | Especificação Corporativa |
|----------|---------------------------|
| **Nó Central** | Embrapa SEDE (Brasília / DF) |
| **Unidades Descentralizadas** | 43 Centros de Pesquisa / UDs em todo o território nacional |
| **Total de Entidades no GLPi** | **44 Entidades** (Estrutura em Arvore: Root = Sede; 43 Nós Filhos) |
| **Público Atendido** | Empregados, pesquisadores, bolsistas, estagiários e terceirizados |
| **Volume Estimado de Chamados** | ~15.000 a 25.000 requisições/mês (consolidação nacional) |

Atualmente, a solicitação de reparos, manutenção e serviços de facilidades ocorre por canais heterogêneos (e-mails locais, formulários em papel, aplicativos de mensagens ou contatos verbais). Essa dispersão resulta em:
* Impossibilidade de consolidar indicadores nacionais de manutenção predial;
* Atrasos na resolução de chamados críticos por falta de controle de SLA;
* Retrabalho administrativo e perda de histórico de intervenções nas UDs;
* Dificuldade no gerenciamento de contratos de terceiros (limpeza, segurança e manutenção).

## 1.2 Objetivos do Projeto

### Objetivo Geral
Implantar a plataforma GLPi v11 como solução corporativa unificada de Central de Serviços Compartilhados (CSC) para a gestão de chamados de Serviços Gerais na SEDE e nas 43 Unidades Descentralizadas da Embrapa.

### Objetivos Específicos
1. **Modelar e estruturar 44 Entidades lógicas** no GLPi com isolamento adequado de dados e regras de visibilidade.
2. **Padronizar o Catálogo de Serviços Gerais** em 4 categorias operacionais e 22 subcategorias.
3. **Implantar formulários dinâmicos inteligentes** utilizando o plugin *Formcreator*, reduzindo erros de preenchimento pelos usuários.
4. **Integrar a plataforma com o AD/LDAP corporativo**, permitindo Single Sign-On (SSO) e sincronização contínua de usuários e lotações.
5. **Estabelecer SLAs e OLAs nacionais e regionais** com escalonamento automático de tickets.
6. **Automação de processos via n8n** para envio de alertas via e-mail e mensageria corporativa, além de orquestração de relatórios.
7. **Garantir a expansibilidade da arquitetura** para recepção futura dos módulos de RH, Financeiro e SAC.

## 1.3 Visão do Ecossistema CSC da Embrapa

```text
                               ┌──────────────────────────────────┐
                               │       Usuário Embrapa (Web/App)  │
                               └─────────────────┬────────────────┘
                                                 │
                                                 ▼
                               ┌──────────────────────────────────┐
                               │   GLPi v11 (Portal de Chamados)  │
                               │   - 44 Entidades (SEDE + 43 UDs) │
                               │   - Formcreator (Formulários)    │
                               └─────────┬────────────────┬───────┘
                                         │                │
            ┌────────────────────────────┘                └────────────────────────────┐
            ▼                                                                          ▼
┌─────────────────────────┐                                                 ┌───────────────────────┐
│     Serviços Gerais     │                                                 │  Expansão Futura      │
│ - Limpeza / Conservação │                                                 │ - Recursos Humanos    │
│ - Logística / Segurança │                                                 │ - Financeiro          │
│ - Manutenção / Infra    │                                                 │ - SAC Embrapa         │
│ - Outros                │                                                 │                       │
└─────────────────────────┘                                                 └───────────────────────┘
            │                                                                          │
            └────────────────────────────┬─────────────────────────────────────────────┘
                                         │
                                         ▼
                               ┌──────────────────────────────────┐
                               │   Barramento de Integrações      │
                               │  - AD/LDAP (Autenticação)        │
                               │  - n8n (Orquestração / Alerting) │
                               │  - BookStack (Base KCS)          │
                               └──────────────────────────────────┘
```

---

# Capítulo 2 — Princípios Corporativos e Governança de Licenciamento

## 2.1 Princípios Arquiteturais da Central de Serviços

A implementação do GLPi na Embrapa será guiada por **10 Princípios Corporativos**:

1. **Multientidade por Padrão**: Toda a parametrização deve respeitar a hierarquia corporativa de 44 entidades, evitando soluções centralizadas monolíticas.
2. **Autonomia Local com Governança Central**: As UDs possuem autonomia operacional para gerenciar suas equipes técnicas locais, mas seguem a taxonomia e as regras corporativas definidas pela SEDE.
3. **Simplicidade na Experiência do Usuário (UX)**: O usuário final deve abrir um chamado com no máximo 3 cliques através de formulários dinâmicos e intuitivos.
4. **Decisão Baseada em Governança, Não Apenas em Licença**: Conforme a análise estratégica do projeto (`001 - Primeiras impressões.pdf`), o sucesso do GLPi em ambiente corporativo depende do nível de governança de TI aplicada (DevOps, backup, HA, monitoramento), seja no modelo Community ou Network.
5. **Rastreabilidade e Auditabilidade**: Todas as ações, alterações de status e atribuições em chamados devem ser registradas em log auditável.
6. **Orientação a SLA e OLA**: Todo serviço do catálogo deve possuir tempo máximo de primeira resposta e tempo de solução acordado.
7. **Integração sem Acoplamento Rígido**: As integrações com AD/LDAP e BookStack serão mediadas por APIs REST e orquestradas preferencialmente via n8n.
8. **Reaproveitamento de Conhecimento**: Soluções de chamados recorrentes devem obrigatoriamente alimentar a Base de Conhecimento KCS.
9. **Segurança e Conformidade (LGPD)**: Proteção de dados pessoais de solicitantes e restrição de acesso por perfil (RBAC).
10. **Escalabilidade Continuada**: A infraestrutura física/virtual deve suportar o crescimento do volume de tickets com o ingresso dos novos setores (RH, Financeiro, SAC).

## 2.2 Análise de Licenciamento: GLPi Community vs GLPI Network Subscription

Com base no levantamento técnico dos materiais de referência (`Souscription-EN.pdf` e portal oficial do GLPi), foi realizada uma análise comparativa para a tomada de decisão executiva da Embrapa:

| Critério | GLPi Community Edition | GLPI Network Subscription (Basic / Standard / Advanced) |
|----------|------------------------|---------------------------------------------------------|
| **Custo de Licença** | Gratuito (Open Source / GPLv3) | Comercial (€1.200 a €12.000+/ano dependendo do porte) |
| **Suporte Técnico** | Comunidade / Equipe Interna de TI da Embrapa | Suporte oficial Nível 3 com SLA garantido pelo fabricante (Teclib') |
| **Plugins Essenciais (Formcreator, Escalade, PDF, Data Injection, Tags, Metabase)** | **Disponíveis Gratuitamente** na comunidade | Incluídos e com suporte oficial do fabricante |
| **Plugins Exclusivos (WhatsApp, OAuth SSO avançado, Branding, GLPI-AI, SCIM, Splitcat)** | Não inclusos na versão pura (necessitam dev próprio ou plugins alternativos) | Incluídos na assinatura |
| **Indicado para a Embrapa** | **SIM (Opção Recomendada para o Kick-off)** | Opção de transição caso haja necessidade futura de suporte N3 homologado |

> [!IMPORTANT]
> **Recomendação Estratégica**: A Embrapa adotará inicialmente o **GLPi 11 Community Edition**. O valor financeiro economizado em licenças será investido na estruturação de uma **Governança Interna de Excelência** (servidores redundantes, rotinas de staging, monitoramento de saúde do servidor e capacitação das equipes locais), garantindo autonomia e alta disponibilidade.

## 2.3 Requisitos de Alta Disponibilidade e DevOps

Para assegurar estabilidade de nível corporativo (*Enterprise Grade*), a infraestrutura do GLPi contará com:

* **Ambiente Duplo (Staging e Production)**: Toda atualização de versão do GLPi ou instalação de novos plugins será obrigatoriamente testada em ambiente de *Staging* prévio.
* **Cluster de Aplicação**: Servidores web Apache/Nginx balanceados com PHP 8.2+.
* **Banco de Dados Redundante**: MariaDB / MySQL em réplica com failover automático.
* **Backup Automatizado**: Dumps de banco diários e cópia de anexos com retenção incremental de 30 dias off-site.
* **Monitoramento e Saúde da Aplicação 24x7**: Checagem de disponibilidade HTTP, consumo de memória PHP, fila de e-mails, latência do banco de dados e armazenamento do diretório `/files`.

---

# Capítulo 3 — Arquitetura Multientidades (44 Entidades) e Gestão de Acessos

## 3.1 Hierarquia de Entidades (SEDE + 43 UDs)

O GLPi utiliza o conceito de **Entidades** para permitir o isolamento multitenant de dados dentro de uma única instalação. A estrutura corporativa da Embrapa será organizada na seguinte árvore:

```text
Embrapa Sede (Root Entity - Código 00)
├── 01. Embrapa Agrobiologia (Seropédica/RJ)
├── 02. Embrapa Agroenergia (Brasília/DF)
├── 03. Embrapa Agropecuária Oeste (Dourados/MS)
├── 04. Embrapa Algodão (Campina Grande/PB)
├── 05. Embrapa Amapá (Macapá/AP)
├── 06. Embrapa Amazônia Ocidental (Manaus/AM)
├── 07. Embrapa Amazônia Oriental (Belém/PA)
├── 08. Embrapa Arroz e Feijão (Santo Antônio de Goiás/GO)
├── 09. Embrapa Baixada Maranhense (São Luís/MA)
├── 10. Embrapa Caprinos e Ovinos (Sobral/CE)
├── 11. Embrapa Cerrados (Planaltina/DF)
├── 12. Embrapa Clima Temperado (Pelotas/RS)
├── 13. Embrapa Cocais (São Luís/MA)
├── 14. Embrapa Gado de Corte (Campo Grande/MS)
├── 15. Embrapa Gado de Leite (Juiz de Fora/MG)
├── 16. Embrapa Instrumentação (São Carlos/SP)
├── 17. Embrapa Mandioca e Fruticultura (Cruz das Almas/BA)
├── 18. Embrapa Meio Ambiente (Jaguariúna/SP)
├── 19. Embrapa Meio-Norte (Teresina/PI)
├── 20. Embrapa Milho e Sorgo (Sete Lagoas/MG)
├── 21. Embrapa Pantanal (Corumbá/MS)
├── 22. Embrapa Pecuária Sudeste (São Carlos/SP)
├── 23. Embrapa Pecuária Sul (Bagé/RS)
├── 24. Embrapa Pesca e Aquicultura (Palmas/TO)
├── 25. Embrapa Rondônia (Porto Velho/RO)
├── 26. Embrapa Roraima (Boa Vista/RR)
├── 27. Embrapa Semiárido (Petrolina/PE)
├── 28. Embrapa Soja (Londrina/PR)
├── 29. Embrapa Solos (Rio de Janeiro/RJ)
├── 30. Embrapa Suínos e Aves (Concórdia/SC)
├── 31. Embrapa Tabuleiros Costeiros (Aracaju/SE)
├── 32. Embrapa Trigo (Passo Fundo/RS)
├── 33. Embrapa Uva e Vinho (Bento Gonçalves/RS)
├── 34. Embrapa Agroindústria de Alimentos (Rio de Janeiro/RJ)
├── 35. Embrapa Agroindústria Tropical (Fortaleza/CE)
├── 36. Embrapa Digital (Campinas/SP)
├── 37. Embrapa Instrumentação Agropecuária (São Carlos/SP)
├── 38. Embrapa Meio Ambiente e Gestão (Brasília/DF)
├── 39. Embrapa Recursos Genéticos e Biotecnologia (Brasília/DF)
├── 40. Embrapa Territorial (Campinas/SP)
├── 41. Embrapa Vinhos e Bebidas (Bento Gonçalves/RS)
├── 42. Embrapa Pecuária e Florestas (Campo Grande/MS)
└── 43. Embrapa Estudos Estratégicos (Brasília/DF)
```

### Regras de Herança e Escopo
* **Visibilidade Recursiva**: Elementos globais (Catálogo de Serviços Corporativo, Perfis, Regras Globais de Negócio, SLAs padrão) criados na **Embrapa Sede** são herdados automaticamente por todas as 43 UDs com sub-entidades ativas (`is_recursive = YES`).
* **Isolamento de Chamados**: Um técnico localizado na *Embrapa Soja (UD 28)* visualizará por padrão apenas os chamados abertos para a sua unidade física, garantindo privacidade e organização local.
* **Visibilidade Consolidada Corporativa**: A equipe de Governança de Serviços Gerais na *Embrapa Sede* possui visão global (recursiva) sobre todas as 44 entidades para geração de relatórios de desempenho e auditoria.

## 3.2 Perfis de Acesso e Matriz de Permissões (RBAC)

O acesso ao sistema será estruturado em **5 Perfis Principais**:

| Perfil GLPi | Público Alvo | Escopo de Visibilidade | Permissões Principais |
|-------------|--------------|────────────────────────|───────────────────────|
| **Self-Service / Requerente** | Todos os Colaboradores da Embrapa | Próprios chamados | Abrir chamados via Formcreator, acompanhar status, responder interações, aprovar/validar solução. |
| **Técnico Local de Serviços Gerais** | Equipe Operacional da UD (Prestadores / Empregados) | Chamados da sua Entidade/UD | Atribuir chamados a si, atualizar status, registrar solução técnica, apontar horas/custos. |
| **Supervisor de Serviços Gerais (UD)** | Gestor de Facilidades local da UD | Chamados da sua Entidade/UD | Reatribuir chamados, aprovar requisições de maior impacto, acompanhar SLAs locais, emitir relatórios da UD. |
| **Gestor Corporativo de Serviços Gerais** | Coordenadoria Geral de Infraestrutura (SEDE) | Global (44 Entidades - Recursivo) | Visualizar relatórios nacionais, alterar parâmetros globais de SLA, gerenciar catálogo unificado. |
| **Administrador do Sistema** | Equipe de TI / Sustentação do GLPi | Global (Configuração do Sistema) | Gerenciar infraestrutura, plugins, integrações AD/n8n, regras de negócio e dicionários. |

---

# Capítulo 4 — Catálogo de Serviços Gerais e Desenho de Processos

## 4.1 Escopo Inicial: Categorias e Catálogo de Serviços Gerais

O catálogo inicial contempla 4 categorias funcionais divididas em 22 subcategorias padronizadas para atendimento em todas as unidades da Embrapa:

```text
Catálogo de Serviços Gerais
├── 1. LIMPEZA/CONSERVAÇÃO
│   ├── Alvenaria
│   ├── Chaveiro
│   ├── Resíduos Sólidos
│   ├── Limpeza-Higienização Predial
│   └── Pintura
├── 2. LOGÍSTICA/SEGURANÇA
│   ├── Acesso ao Parque
│   ├── Almoxarifado
│   ├── Autorização para Condução
│   ├── Incidente no Estacionamento
│   ├── Auditórios e Espaços Comuns
│   ├── Cadastro de Veículo
│   ├── Agendamento de Veículo
│   └── Cadastro no SISDAP
├── 3. MANUTENÇÃO/INFRAESTRUTURA
│   ├── Ar Condicionado
│   ├── Cabeamento Lógico
│   ├── Marcenaria
│   ├── Paisagismo Interno
│   ├── Elevador
│   ├── Hidráulica
│   ├── Rede de Telefonia
│   └── Rede Elétrica
└── 4. OUTROS
    └── Serviço Não Classificado
```

### Catálogo Detalhado de Serviços e SLAs Padrão

| Categoria | Subcategoria | Tipo de Serviço | Visibilidade | Unidade | Urgência | Impacto | Prioridade | Tempo Resposta (TTR) | Tempo SLA (TTO) |
|-----------|--------------|-----------------|--------------|---------|----------|---------|------------|----------------------|-----------------|
| **LIMPEZA/CONSERVAÇÃO** | ALVENARIA | Solicitação de Serviço | Portal Autoatendimento | Todas | Baixa | Muito Baixo | Prioridade 5 | 2h | 5 Dias |
| **LIMPEZA/CONSERVAÇÃO** | CHAVEIRO | Solicitação de Serviço | Portal Autoatendimento | Todas | Baixa | Muito Baixo | Prioridade 5 | 2h | 5 Dias |
| **LIMPEZA/CONSERVAÇÃO** | RESÍDUOS SÓLIDOS | Solicitação de Serviço | Portal Autoatendimento | Todas | Normal | Normal | Prioridade 3 | 2h | 8h |
| **LIMPEZA/CONSERVAÇÃO** | LIMPEZA-HIGIENIZAÇÃO PREDIAL | Solicitação de Serviço | Portal Autoatendimento | Todas | Normal | Normal | Prioridade 3 | 2h | 8h |
| **LIMPEZA/CONSERVAÇÃO** | PINTURA | Solicitação de Serviço | Portal Autoatendimento | Todas | Muito Baixa | Baixo | Prioridade 5 | 2h | 5 Dias |
| **LOGÍSTICA/SEGURANÇA** | ACESSO AO PARQUE | Solicitação de Serviço | Portal Autoatendimento | Todas | Normal | Baixo | Prioridade 4 | 2h | 2 Dias |
| **LOGÍSTICA/SEGURANÇA** | ALMOXARIFADO | Solicitação de Serviço | Portal Autoatendimento | Todas | Normal | Normal | Prioridade 3 | 2h | 8h |
| **LOGÍSTICA/SEGURANÇA** | AUTORIZAÇÃO PARA CONDUÇÃO | Solicitação de Serviço | Portal Autoatendimento | Todas | Normal | Baixo | Prioridade 4 | 2h | 2 Dias |
| **LOGÍSTICA/SEGURANÇA** | INCIDENTE NO ESTACIONAMENTO | Solicitação de Serviço | Portal Autoatendimento | Todas | Normal | Muito Baixo | Prioridade 4 | 2h | 2 Dias |
| **LOGÍSTICA/SEGURANÇA** | AUDITÓRIOS E ESPAÇOS COMUNS | Solicitação de Serviço | Portal Autoatendimento | Todas | Normal | Muito Baixo | Prioridade 4 | 2h | 2 Dias |
| **LOGÍSTICA/SEGURANÇA** | CADASTRO DE VEÍCULO | Solicitação de Serviço | Portal Autoatendimento | Todas | Normal | Baixo | Prioridade 4 | 2h | 2 Dias |
| **LOGÍSTICA/SEGURANÇA** | AGENDAMENTO DE VEÍCULO | Solicitação de Serviço | Portal Autoatendimento | Todas | Normal | Baixo | Prioridade 4 | 2h | 2 Dias |
| **LOGÍSTICA/SEGURANÇA** | CADASTRO NO SISDAP | Solicitação de Serviço | Portal Autoatendimento | Todas | Normal | Muito Baixo | Prioridade 4 | 2h | 2 Dias |
| **MANUTENÇÃO/INFRAESTRUTURA** | AR CONDICIONADO | Solicitação de Serviço | Portal Autoatendimento | Todas | Baixa | Muito Baixo | Prioridade 5 | 2h | 5 Dias |
| **MANUTENÇÃO/INFRAESTRUTURA** | CABEAMENTO LÓGICO | Solicitação de Serviço | Portal Autoatendimento | Todas | Baixa | Muito Baixo | Prioridade 5 | 2h | 5 Dias |
| **MANUTENÇÃO/INFRAESTRUTURA** | MARCENARIA | Solicitação de Serviço | Portal Autoatendimento | Todas | Baixa | Muito Baixo | Prioridade 5 | 2h | 5 Dias |
| **MANUTENÇÃO/INFRAESTRUTURA** | PAISAGISMO INTERNO | Solicitação de Serviço | Portal Autoatendimento | Todas | Normal | Baixo | Prioridade 4 | 2h | 2 Dias |
| **MANUTENÇÃO/INFRAESTRUTURA** | ELEVADOR | Solicitação de Serviço | Portal Autoatendimento | Todas | Normal | Normal | Prioridade 3 | 2h | 8h |
| **MANUTENÇÃO/INFRAESTRUTURA** | HIDRÁULICA | Solicitação de Serviço | Portal Autoatendimento | Todas | Baixa | Muito Baixo | Prioridade 5 | 2h | 5 Dias |
| **MANUTENÇÃO/INFRAESTRUTURA** | REDE DE TELEFONIA | Solicitação de Serviço | Portal Autoatendimento | Todas | Baixa | Muito Baixo | Prioridade 5 | 2h | 5 Dias |
| **MANUTENÇÃO/INFRAESTRUTURA** | REDE ELÉTRICA | Solicitação de Serviço | Portal Autoatendimento | Todas | Baixa | Muito Baixo | Prioridade 5 | 2h | 5 Dias |
| **OUTROS** | SERVIÇO NÃO CLASSIFICADO | Solicitação de Serviço | Portal Autoatendimento | Todas | Normal | Baixo | Prioridade 4 | 2h | 2 Dias |

## 4.2 Formulários Dinâmicos Inteligentes (Formcreator)

Para evitar chamados abertos com descrições incompletas ("minha torneira quebrou" sem indicar o bloco ou sala), será utilizado o plugin **Formcreator**.

### Exemplo de Fluxo do Formulário: *Manutenção / Infraestrutura (ex: Hidráulica)*
1. **Seleção da Entidade**: Preenchida automaticamente com base na lotação do usuário no AD.
2. **Localização Precisa**:
   - Bloco / Prédio (Menu Dropdown)
   - Pavimento / Andar (Menu Dropdown)
   - Sala / Laboratório (Campo de Texto Obrigatório)
3. **Tipo de Ocorrência**:
   - Vazamento em torneira/pia
   - Entupimento sanitário
   - Problema em caixa d'água / bomba
4. **Criticidade Percebida**:
   - Baixa (não impede o uso do ambiente)
   - Média (compromete parcialmente)
   - Alta / Emergência (risco de alagamento ou interrupção de laboratório)
5. **Anexo de Evidência**: Upload obrigatório ou recomendado de foto do local/problema.

## 4.3 Matriz de Expansão Futura (Fase 2+)

A arquitetura do GLPi foi projetada para receber novos módulos de atendimento sem necessidade de alterar a estrutura técnica básica. O plano de expansão prevê:

```text
                           ┌─────────────────────────────────────────┐
                           │      GLPi Central de Serviços (CSC)     │
                           └────────────────────┬────────────────────┘
                                                │
          ┌──────────────────────┬──────────────┴───────┬──────────────────────┐
          ▼                      ▼                      ▼                      ▼
┌───────────────────┐  ┌───────────────────┐  ┌───────────────────┐  ┌───────────────────┐
│  Serviços Gerais  │  │ Recursos Humanos  │  │   Financeiro      │  │    SAC Embrapa    │
│    (FASE 1)       │  │    (FASE 2)       │  │    (FASE 3)       │  │    (FASE 4)       │
├───────────────────┤  ├───────────────────┤  ├───────────────────┤  ├───────────────────┤
│ - Limpeza/Conserv.│  │ - Férias / Folha  │  │ - Reembolsos      │  │ - Atendimento ao  │
│ - Logíst./Segur.  │  │ - Benefícios      │  │ - Viagens/Diárias │  │   Cidadão         │
│ - Manut./Infra.   │  │ - Avaliação Desem.│  │ - Compras/Contratos│  │ - Ouvidoria       │
│ - Outros          │  │                   │  │                   │  │   Técnicas        │
└───────────────────┘  └───────────────────┘  └───────────────────┘  └───────────────────┘
```

---

# Capítulo 5 — Integrações Técnicas e Ecossistema Corporativo

## 5.1 Autenticação e Sincronização AD/LDAP Corporativo

O GLPi será integrado ao **Active Directory (AD/LDAP)** central da Embrapa para garantir Single Sign-On e atualização diária do cadastro de colaboradores:

```text
┌────────────────────────┐      LDAPS (Porta 636)      ┌────────────────────────┐
│  AD / LDAP Corporativo ├────────────────────────────►│  GLPi v11 (Embrapa)    │
│  (Sede + UDs)          │  Sync Diário de Usuários    │  - Importação de Nome  │
└────────────────────────┘  e Grupos por Entidade      │  - E-mail, Matrícula   │
                                                       │  - Lotação (Entidade)  │
                                                       └────────────────────────┘
```

* **Mapeamento de Atributos**:
  * `samaccountname` -> Nome de Usuário GLPi
  * `mail` -> E-mail Corporativo
  * `telephonenumber` -> Telefone / Ramal
  * `physicalDeliveryOfficeName` -> Entidade / UD Pertencente
* **Regras Automáticas de Atribuição de Entidade**: O GLPi executará regras de negócio na importação para vincular o usuário diretamente à sua Entidade/UD correspondente com base na OU (Organizational Unit) do AD.

## 5.2 Automação e Orquestração via n8n

O **n8n** será utilizado como a camada de integração low-code para automatizar fluxos complexos fora do GLPi:

1. **Notificações Avançadas**: Envio de alertas de chamados urgentes para grupos de manutenção via Microsoft Teams ou WhatsApp.
2. **Consolidação de Relatórios Exectivos**: Extração semanal via API REST do GLPi e envio automatizado de dashboards em PDF para os Chefes Adjuntos de Administração (CHADs) de cada UD.
3. **Sincronização com Sistemas Legados**: Integração com sistemas de patrimônio ou RH da Embrapa.

## 5.3 Monitoramento da Infraestrutura e Saúde do Sistema

A infraestrutura do GLPi será monitorada 24x7 por rotinas automáticas de infraestrutura da TI Corporativa:

* **Checagens de Aplicação**: Latência da API, disponibilidade da interface web e tempo de resposta das consultas SQL.
* **Checagens de Sistema**: Espaço em disco do diretório de anexos (`/files`), consumo de CPU/Memória PHP-FPM e status de execução das tarefas agendadas (GLPi cron).
* **Alertas de Sustentação**: Notificação automática das equipes de infraestrutura TI em caso de degradação de desempenho ou desconexão do banco de dados.

---

# Capítulo 6 — Governança Operacional, SLAs e Indicadores (KPIs)

## 6.1 Matriz de Priorização (Impacto x Urgência)

A prioridade de cada chamado é calculada automaticamente combinando a urgência declarada com o impacto operacional do problema:

| Urgência \ Impacto | Baixo (Individual) | Médio (Setor/Laboratório) | Alto (Toda a UD / Sede) |
|--------------------|--------------------+---------------------------|─────────────────────────|
| **Baixa** | 1 — Muito Baixa | 2 — Baixa | 3 — Média |
| **Média** | 2 — Baixa | 3 — Média | 4 — Alta |
| **Alta** | 3 — Média | 4 — Alta | 5 — Muito Alta (Emergência) |

## 6.2 Tempos Alvo de SLA (Serviços Gerais)

Conforme a parametrização oficial do Catálogo de Serviços Gerais, o **Tempo de Primeira Resposta (TTR)** é padronizado em **2 horas** para todas as solicitações, variando o **Tempo de Solução (SLA / TTO)** conforme o nível de prioridade e a complexidade do serviço:

| Nível de Prioridade | Tempo de Resposta (TTR) | Tempo de Solução (SLA / TTO) | Serviços Abrangidos (Exemplos) |
|---------------------|-------------------------|------------------------------|--------------------------------|
| **Prioridade 3 (Normal / Normal)** | 2 horas | 8 horas | Resíduos Sólidos, Limpeza-Higienização Predial, Almoxarifado, Elevador |
| **Prioridade 4 (Normal / Baixo ou Muito Baixo)** | 2 horas | 2 dias | Acesso ao Parque, Autorização para Condução, Incidente no Estacionamento, Auditórios e Espaços Comuns, Cadastro/Agendamento de Veículo, Cadastro SISDAP, Paisagismo Interno, Serviço Não Classificado |
| **Prioridade 5 (Baixa / Muito Baixo ou Baixo)** | 2 horas | 5 dias | Alvenaria, Chaveiro, Pintura, Ar Condicionado, Cabeamento Lógico, Marcenaria, Hidráulica, Rede de Telefonia, Rede Elétrica |

## 6.3 Indicadores Chave de Desempenho (KPIs)

A gestão da Central de Serviços monitorará mensalmente os seguintes indicadores consolidados por Entidade e Nível Nacional:

1. **MTTR (Mean Time to Repair / Tempo Médio de Solução)**: Tempo médio entre a abertura e a resolução do chamado.
2. **Taxa de Cumprimento de SLA (%)**: Percentual de chamados resolvidos dentro do prazo acordado (Meta: >= 92%).
3. **FCR (First Contact Resolution / Resolução no Primeiro Contato)**: Chamados resolvidos imediatamente na primeira intervenção técnica (Meta: >= 35%).
4. **Índice de Satisfação do Usuário (CSAT)**: Avaliação média atribuída pelo solicitante na pesquisa de encerramento (Escala 1 a 5 - Meta: >= 4.5).
5. **Volume de Chamados por Entidade / Categoria**: Mapeamento dos gargalos operacionais e infraestrutura mais demandada.

---

# Capítulo 7 — Integração com a Gestão do Conhecimento (KCS / BookStack)

## 7.1 O Ciclo KCS em Serviços Gerais

Seguindo o padrão estabelecido na *Arquitetura Corporativa de Gestão do Conhecimento da Embrapa*, a resolução de chamados de Serviços Gerais alimentará continuamente a Base de Conhecimento:

```text
┌────────────────────────┐      Encerramento do Ticket      ┌────────────────────────┐
│  Chamado Resolvido no  ├─────────────────────────────────►│  Solicitação de Novo   │
│  GLPi (Serviços Gerais)│  Solução Técnica Registrada      │  Artigo de Conhecimento│
└────────────────────────┘                                  └───────────┬────────────┘
                                                                        │
                                                                        ▼
                                                            ┌────────────────────────┐
                                                            │  BookStack Corporativo │
                                                            │  - Estante: Serviços   │
                                                            │  - Livro: Manutenção   │
                                                            └────────────────────────┘
```

1. **Captura do Conhecimento**: Ao resolver um chamado complexo ou recorrente (ex: procedimento padrão para religamento de bomba d'água modelo X), o técnico registra a solução estruturada no GLPi.
2. **Revisão e Publicação**: Casos com alto potencial de reutilização são convertidos em artigos na estante de *Serviços Gerais* do **BookStack** através do fluxo KCS.
3. **Autoatendimento**: Usuários finais consultam a base de conhecimento antes de abrir chamados, reduzindo demandas repetitivas (Deflexão de Chamados).

---

# Capítulo 8 — Roadmap de Implementação e Plano de Fases

A implementação do GLPi na Embrapa será executada em **6 Fases Progressivas**, totalizando um cronograma estimado de **12 meses** para a maturidade completa:

```text
Fase 1: Infraestrutura & Entidades (Mês 1-2)
  ├── Provisionamento do Cluster de Servidores (Staging e Prod)
  ├── Instalação do GLPi v11 Community Edition
  ├── Configuração da Árvore de 44 Entidades (SEDE + 43 UDs)
  └── Integração LDAP/AD para Autenticação Unificada

Fase 2: Catálogo de Serviços Gerais & Formulários (Mês 3-4)
  ├── Parametrização dos 6 Domínios de Serviços Gerais
  ├── Construção de Formulários Dinâmicos com Formcreator
  ├── Definição de Perfis (RBAC), Grupos Técnicos e Matriz de SLAs
  └── Configuração do Motor de Notificações por E-mail

Fase 3: Projeto Piloto — SEDE + 3 UDs (Mês 5-6)
  ├── Implantação em Produção na SEDE e em 3 UDs Piloto (ex: Cerrados, Soja, Agrobiologia)
  ├── Capacitação das Equipes Locais e Testes de Aceitação de Usuário (TAU)
  └── Ajustes Finais de Processo e Usabilidade

Fase 4: Rollout Nacional — 40 UDs (Mês 7-9)
  ├── Ativação Sequencial das 40 Unidades Descentralizadas Restantes (lotes de 10 UDs/mês)
  ├── Treinamento EAD para Usuários Finais e Técnicos Regionais
  └── Entrada em Operação Oficial da Central de Serviços Gerais Nacionais

Fase 5: Integrações Avançadas & Analytics (Mês 10-11)
  ├── Integração n8n (Notificações Teams/WhatsApp e Relatórios)
  ├── Monitoramento e Saúde de Infraestrutura do GLPi
  ├── Conexão KCS com BookStack Corporativo
  └── Publicação dos Dashboards Nacionais de KPI para Diretoria Executiva

Fase 6: Expansão de Escopo — RH, Financeiro e SAC (Mês 12+)
  ├── Mapeamento de Processos de Recursos Humanos
  ├── Mapeamento de Processos Financeiros e Suprimentos
  └── Integração do SAC Corporativo da Embrapa
```

---

# Considerações Finais

O projeto de implementação do **GLPi v11** para os Serviços Gerais da Embrapa representa um marco decisivo na modernização administrativa da instituição. Ao unificar 44 Entidades sob uma única plataforma estruturada de Central de Serviços Compartilhados (CSC), a Embrapa alcançará:

* **Padronização Nacional**: Processos homogêneos de atendimento da SEDE às UDs mais remotas.
* **Eficiência e Redução de Custos**: Melhor gestão de contratos de facilidades e manutenção preventiva.
* **Governança de TI e Dados**: Decisões baseadas em indicadores reais de desempenho e disponibilidade de infraestrutura.
* **Prontidão para o Futuro**: Base sólida e testada pronta para absorver as operações de RH, Financeiro e SAC nos anos subsequentes.

---

# Anexo A — Relação das 44 Entidades Corporativas da Embrapa

| Código GLPi | Nome da Entidade / Unidade | Sigla | Cidade / UF | Papel na Hierarquia |
|-------------|----------------------------|-------|-------------|---------------------|
| `00` | **Embrapa Sede (Administração Central)** | SEDE | Brasília / DF | **Root Entity (Nó Pai)** |
| `01` | Embrapa Agrobiologia | CNPAB | Seropédica / RJ | Entidade Filha |
| `02` | Embrapa Agroenergia | UEPAE | Brasília / DF | Entidade Filha |
| `03` | Embrapa Agropecuária Oeste | CPAO | Dourados / MS | Entidade Filha |
| `04` | Embrapa Algodão | CNPA | Campina Grande / PB | Entidade Filha |
| `05` | Embrapa Amapá | CPAFAP | Macapá / AP | Entidade Filha |
| `06` | Embrapa Amazônia Ocidental | CPAA | Manaus / AM | Entidade Filha |
| `07` | Embrapa Amazônia Oriental | CPATU | Belém / PA | Entidade Filha |
| `08` | Embrapa Arroz e Feijão | CNPAF | Santo Antônio de Goiás / GO | Entidade Filha |
| `09` | Embrapa Baixada Maranhense | UEP | São Luís / MA | Entidade Filha |
| `10` | Embrapa Caprinos e Ovinos | CNPC | Sobral / CE | Entidade Filha |
| `11` | Embrapa Cerrados | CPAC | Planaltina / DF | Entidade Filha |
| `12` | Embrapa Clima Temperado | CPACT | Pelotas / RS | Entidade Filha |
| `13` | Embrapa Cocais | UEP | São Luís / MA | Entidade Filha |
| `14` | Embrapa Gado de Corte | CNPGC | Campo Grande / MS | Entidade Filha |
| `15` | Embrapa Gado de Leite | CNPGL | Juiz de Fora / MG | Entidade Filha |
| `16` | Embrapa Instrumentação | CNPDIA | São Carlos / SP | Entidade Filha |
| `17` | Embrapa Mandioca e Fruticultura | CNPMF | Cruz das Almas / BA | Entidade Filha |
| `18` | Embrapa Meio Ambiente | CNPMA | Jaguariúna / SP | Entidade Filha |
| `19` | Embrapa Meio-Norte | CPAMN | Teresina / PI | Entidade Filha |
| `20` | Embrapa Milho e Sorgo | CNPMS | Sete Lagoas / MG | Entidade Filha |
| `21` | Embrapa Pantanal | CPAP | Corumbá / MS | Entidade Filha |
| `22` | Embrapa Pecuária Sudeste | CPPSE | São Carlos / SP | Entidade Filha |
| `23` | Embrapa Pecuária Sul | CPPSUL | Bagé / RS | Entidade Filha |
| `24` | Embrapa Pesca e Aquicultura | CNPASA | Palmas / TO | Entidade Filha |
| `25` | Embrapa Rondônia | CPAFRO | Porto Velho / RO | Entidade Filha |
| `26` | Embrapa Roraima | CPAFRR | Boa Vista / RR | Entidade Filha |
| `27` | Embrapa Semiárido | CPATSA | Petrolina / PE | Entidade Filha |
| `28` | Embrapa Soja | CNPSO | Londrina / PR | Entidade Filha |
| `29` | Embrapa Solos | CNPS | Rio de Janeiro / RJ | Entidade Filha |
| `30` | Embrapa Suínos e Aves | CNPSA | Concórdia / SC | Entidade Filha |
| `31` | Embrapa Tabuleiros Costeiros | CPATC | Aracaju / SE | Entidade Filha |
| `32` | Embrapa Trigo | CNPT | Passo Fundo / RS | Entidade Filha |
| `33` | Embrapa Uva e Vinho | CNPUV | Bento Gonçalves / RS | Entidade Filha |
| `34` | Embrapa Agroindústria de Alimentos | CTAA | Rio de Janeiro / RJ | Entidade Filha |
| `35` | Embrapa Agroindústria Tropical | CNPAT | Fortaleza / CE | Entidade Filha |
| `36` | Embrapa Digital | CNPTIA | Campinas / SP | Entidade Filha |
| `37` | Embrapa Instrumentação Agropecuária | CNPDIA-II | São Carlos / SP | Entidade Filha |
| `38` | Embrapa Meio Ambiente e Gestão | HQ-MAG | Brasília / DF | Entidade Filha |
| `39` | Embrapa Recursos Genéticos e Biotecnologia | CENARGEN | Brasília / DF | Entidade Filha |
| `40` | Embrapa Territorial | CNPM | Campinas / SP | Entidade Filha |
| `41` | Embrapa Vinhos e Bebidas | CNPUV-II | Bento Gonçalves / RS | Entidade Filha |
| `42` | Embrapa Pecuária e Florestas | CNPGC-FL | Campo Grande / MS | Entidade Filha |
| `43` | Embrapa Estudos Estratégicos | AGEE | Brasília / DF | Entidade Filha |

---

# Anexo B — Matriz Técnica de Plugins e Funcionalidades GLPi

| Plugin / Recurso | Origem / Modalidade | Função na Arquitetura Embrapa | Prioridade de Implantação |
|------------------|---------------------|--------------------------------|---------------------------|
| **Formcreator** | Community (Grátis) / Network | Construção de formulários dinâmicos e catálogo de serviços | **Fase 1 (Crítico)** |
| **LDAP / AD Sync** | Nativo do GLPi Core | Sincronização de usuários e autenticação unificada | **Fase 1 (Crítico)** |
| **Escalade** | Community / Network | Escalonamento automático de chamados por tempo de SLA | **Fase 2 (Alto)** |
| **Fields** | Community / Network | Adição de campos personalizados em chamados e ativos | **Fase 2 (Médio)** |
| **Tags** | Community / Network | Etiquetagem e categorização ágil de demandas | **Fase 2 (Médio)** |
| **Metabase Integration** | Community / Network | Conexão com ferramenta BI para dashboards avançados | **Fase 5 (Médio)** |
| **OAuth IMAP / SSO** | Community / Network | Autenticação moderna via e-mail corporativo / Azure | **Fase 2 (Alto)** |
| **WhatsApp Notifications** | GLPI Network Exclusive / n8n | Notificação instantânea via mensageria (via n8n) | **Fase 5 (Desejável)** |

---

# Anexo C — Glossário de Siglas e Termos Corporativos

* **AD (Active Directory)**: Serviço de diretório da Microsoft utilizado para autenticação e gestão de usuários.
* **CHAD**: Chefia Adjunta de Administração das Unidades Descentralizadas da Embrapa.
* **CSC (Central de Serviços Compartilhados)**: Modelo operacional que centraliza funções de suporte administrativo e operacional.
* **CSAT (Customer Satisfaction Score)**: Métrica de avaliação de satisfação do usuário.
* **ESM (Enterprise Service Management)**: Aplicação dos conceitos de gestão de serviços de TI (ITSM) para outras áreas da empresa (Serviços Gerais, RH, Financeiro).
* **FCR (First Contact Resolution)**: Resolução de um chamado no primeiro contato técnico.
* **GLPi**: *Gestionnaire Libre de Parc Informatique* — Plataforma Open Source de Gestão de Serviços e Ativos.
* **KCS (Knowledge-Centered Service)**: Metodologia focada na criação e manutenção continuada de conhecimento através da operação de atendimento.
* **MTTR (Mean Time to Repair)**: Tempo médio decorrido para reparar/solucionar um incidente.
* **OLA (Operational Level Agreement)**: Acordo de Nível Operacional firmado entre equipes internas.
* **RBAC (Role-Based Access Control)**: Controle de acesso baseado em papéis e perfis de usuário.
* **SLA (Service Level Agreement)**: Acordo de Nível de Serviço estabelecido entre os prestadores de serviço e os usuários.
* **UD (Unidade Descentralizada)**: Centros de Pesquisa regionais ou temáticos integrantes da Embrapa.
