---
name: cria-techspec
description: Cria Especificações Técnicas a partir de um PRD existente, traduzindo requisitos de produto em decisões arquiteturais e orientação de implementação. Realiza análise profunda do projeto, pesquisa técnica, pesquisa regras de negócio e esclarecimento com o usuário. Use quando o usuário solicitar criar uma spec técnica, definir arquitetura ou planejar implementação de uma funcionalidade com PRD existente. Não use para criação de PRD, detalhamento de tarefas ou implementação direta de código.
---

# Criação de Tech Spec

## Procedimentos

**Passo 1: Validar Pré-requisitos**
1. Confirme que o slug da funcionalidade foi fornecido.
2. Verifique se o PRD existe em `ai-sdd/prd-[feature-slug]/prd.md`. Se ausente, interrompa e direcione o usuário para a skill `cria-prd`.
3. Verifique a existência de documentos de contexto (não bloqueia, mas enriquece a spec):
   - `./ai-sdd/system/vision.md` — propósito e proposta de valor.
   - `./ai-sdd/system/product_map.md` — fluxos de usuário relacionados.
   - `./ai-sdd/system/roadmap.md` — fase correspondente da funcionalidade.

**Passo 2: Analisar PRD (Obrigatório)**
1. Leia o PRD completamente — NÃO pule este passo.
2. Identifique: requisitos funcionais, restrições de alto nível e métricas de sucesso.
3. Extraia requisitos principais que impactam decisões arquiteturais.
4. Anote questões técnicas que surgem da leitura do PRD.

**Passo 3: Análise Profunda do Projeto (Obrigatório)**
1. Explore a base de código para mapear:
   - **Estrutura**: Arquivos, módulos, diretórios e organização geral.
   - **Dependências**: Gems, bibliotecas e serviços utilizados.
   - **Padrões**: Convenções de código, patterns arquiteturais existentes.
   - **Pontos de Integração**: Controllers, models, views, jobs e mailers relevantes.
2. Identifique código existente que pode ser reutilizado ou estendido.
3. Mapeie caminhos críticos e áreas de impacto da nova funcionalidade.

**Passo 4: Pesquisa (Obrigatório)**
1. Execute pesquisas web (mínimo 3) para coletar:
   - Regras de negócio e padrões do domínio.
   - Boas práticas de implementação para o tipo de funcionalidade.
   - Documentação de bibliotecas e APIs que serão utilizadas.
2. Complete toda a pesquisa ANTES de fazer perguntas de esclarecimento — as respostas da pesquisa informam as perguntas.

**Passo 5: Esclarecimentos Técnicos (Obrigatório)**
1. Faça perguntas de esclarecimento focadas ao usuário, cobrindo:
   - **Domínio**: Como o negócio opera nesta área? Existem regras especiais?
   - **Fluxo de Dados**: Quais são as entradas, transformações e saídas esperadas?
   - **Integrações**: Existem serviços externos, APIs ou sistemas a interfacear?
   - **Performance**: Existem requisitos de volume, latência ou concorrência?
   - **Segurança**: Existem dados sensíveis, permissões ou conformidade a considerar?
   - **Testes**: Quais cenários são críticos para validação?
2. NÃO prossiga até obter respostas suficientes para tomar decisões arquiteturais.

**Passo 6: Planejar Arquitetura (Obrigatório)**
1. Com base na análise e esclarecimentos, planeje:
   - Componentes principais e suas responsabilidades.
   - Fluxo de dados entre componentes.
   - Decisões arquiteturais chave com justificativas.
   - Trade-offs considerados e alternativas rejeitadas.
2. Apresente o plano ao usuário para alinhamento.
3. Aguarde aprovação do usuário antes de prosseguir para o Passo 7.

**Passo 7: Mapeamento de Conformidade com Standards (Obrigatório)**
1. Identifique skills do projeto em `.opencode/skills/` que se aplicam a esta spec.
2. Verifique aderência ao `AGENTS.md` (convenções, padrões de código, stack).
3. Destaque desvios com justificativa e alternativas conformes.

**Passo 8: Gerar Tech Spec (Obrigatório)**
1. Leia o template em `assets/techspec-template.md`.
2. Siga a estrutura do template sem desvios — cada seção deve ser preenchida.
3. Regras de redação:
   - Foque no **COMO**, não no O QUÊ (o PRD tem o que/porquê).
   - NÃO repita requisitos funcionais do PRD — referencie-os pelo código (RF-XXX).
   - A spec define **especificação**, NÃO código de implementação detalhado.
   - Interfaces e exemplos de código devem ser concisos (≤20 linhas por exemplo).
   - Prefira bibliotecas/gems existentes ao invés de desenvolvimento customizado.
4. Mantenha o documento com ~2.000 palavras.

**Passo 9: Salvar Tech Spec (Obrigatório)**
1. Salve em: `ai-sdd/prd-[feature-slug]/techspec.md`.
2. Se o arquivo já existir, confirme com o usuário antes de sobrescrever.

**Passo 10: Relatar Resultados**
1. Informe o caminho final do arquivo.
2. Resuma: decisões arquiteturais principais, componentes novos/modificados e riscos identificados.

## Princípios Fundamentais
- Explore antes de perguntar; pesquise antes de decidir; planeje antes de redigir.
- Tech Spec foca no COMO — o PRD define o O QUÊ e PORQUÊ.
- Prefira arquitetura simples e evolutiva com interfaces claras.
- Prefira bibliotecas existentes ao invés de soluções customizadas.
- Forneça considerações de testabilidade e observabilidade desde o início.
- Respeite padrões e convenções existentes no projeto.
- O documento deve ser implementável — um desenvolvedor deve conseguir trabalhar a partir dele.
- Para buscas na web, use a ferramenta Web Search.
- Para pesquisas técnicas na web relacionadas a documentação utilize o Context7 MCP.

## Lista de Verificação de Qualidade
- [ ] PRD lido e requisitos principais extraídos.
- [ ] Base de código analisada em profundidade.
- [ ] Pesquisa web concluída (mín. 3 buscas).
- [ ] Perguntas de esclarecimento concluídas e respondidas.
- [ ] Plano arquitetural apresentado e aprovado pelo usuário.
- [ ] Skills e AGENTS.md verificados para conformidade.
- [ ] Tech Spec gerada usando o template.
- [ ] Sem requisitos funcionais repetidos do PRD.
- [ ] Interfaces e exemplos de código concisos.
- [ ] Estratégia de testes definida (unit, integração, E2E).
- [ ] Riscos e mitigações documentados.
- [ ] Arquivo salvo em `./ai-sdd/prd-[feature-slug]/techspec.md`.

## Tratamento de Erros
- Se o PRD não existir, interrompa e direcione o usuário para a skill `cria-prd`.
- Se o usuário fornecer contexto insuficiente, faça perguntas de esclarecimento complementares.
- Se o plano arquitetural for rejeitado, revise com base no feedback e reapresente para aprovação.
- Se o arquivo de saída já existir, confirme com o usuário antes de sobrescrever.
- Se uma biblioteca/gem necessária não estiver disponível, documente como dependência técnica.
