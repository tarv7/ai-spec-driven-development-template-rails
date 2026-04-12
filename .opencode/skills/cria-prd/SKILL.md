---
name: cria-prd
description: Cria Documentos de Requisitos de Produto (PRDs) a partir de solicitações de funcionalidades seguindo um fluxo estruturado de esclarecimento, planejamento e redação. Gera um PRD padronizado usando template, focado no O QUE e PORQUÊ. Use quando o usuário solicitar criar um PRD, definir requisitos ou documentar uma nova funcionalidade. Não use para especificações técnicas, detalhamento de tarefas ou planejamento de implementação.
---

# Criação de PRD

## Procedimentos

**Passo 1: Validar Pré-requisitos**
1. Confirme que o nome ou descrição da funcionalidade foi fornecido pelo usuário.
2. Derive o slug em kebab-case para o diretório de saída: `./ai-sdd/prd-[feature-slug]/`.
3. Verifique a existência dos documentos de contexto (não bloqueia, mas enriquece o PRD):
   - `./ai-sdd/system/vision.md` — propósito e proposta de valor.
   - `./ai-sdd/system/product_map.md` — fluxos de usuário relacionados.
   - `./ai-sdd/system/roadmap.md` — fase correspondente da funcionalidade.
4. Leia os documentos disponíveis para extrair contexto relevante.

**Passo 2: Esclarecer Requisitos (Obrigatório)**
1. Faça perguntas de esclarecimento ao usuário antes de gerar qualquer conteúdo.
2. Cubra todas as áreas da lista de esclarecimento:
   - **Problema e Objetivos**: Qual problema específico resolver? Quais metas mensuráveis de sucesso?
   - **Usuários e Histórias**: Quem são os usuários principais? Quais user stories descrevem o uso?
   - **Funcionalidade Principal**: Quais são as entradas, saídas e ações do usuário? O que o sistema deve fazer?
   - **Escopo e Limites**: O que NÃO está incluído? Quais dependências existem com outras funcionalidades?
   - **Design e Experiência**: Existem diretrizes de UI/UX? Quais requisitos de acessibilidade? Há referências visuais?
   - **Restrições**: Existem mandatos de conformidade, performance ou integrações obrigatórias?
3. NÃO prossiga para o Passo 3 até obter respostas suficientes.

**Passo 3: Planejar o PRD (Obrigatório)**
1. Crie um plano seção por seção do PRD, incluindo:
   - Abordagem para cada seção do template.
   - Áreas que requerem pesquisa adicional (use Web Search para regras de negócio, padrões do setor, etc.).
   - Suposições feitas e dependências identificadas.
2. Apresente o plano ao usuário para alinhamento.
3. Aguarde aprovação do usuário antes de prosseguir para o Passo 4.

**Passo 4: Redigir o PRD (Obrigatório)**
1. Leia o template em `assets/prd-template.md`.
2. Siga a estrutura do template sem desvios — cada seção deve ser preenchida.
3. Regras de redação:
   - Foque no **O QUE** e **PORQUÊ**, nunca no **COMO** (implementação pertence à Tech Spec).
   - Requisitos funcionais devem ser **numerados** para rastreabilidade.
   - Requisitos devem ser **verificáveis** — evite linguagem vaga como "rápido", "fácil", "intuitivo" sem critérios.
   - User stories no formato: "Como [tipo de usuário], eu quero [ação] para que [benefício]".
   - Restrições técnicas devem ser de **alto nível** — sem decisões de design ou arquitetura.
4. Mantenha o documento com menos de 2000 palavras.

**Passo 5: Salvar o PRD (Obrigatório)**
1. Crie o diretório: `./ai-sdd/prd-[feature-slug]/`.
2. Salve o PRD em: `./ai-sdd/prd-[feature-slug]/prd.md`.
3. Se o diretório já existir e contiver um PRD, confirme com o usuário antes de sobrescrever.

**Passo 6: Relatar Resultados**
1. Informe o caminho final do arquivo.
2. Resuma: objetivo da funcionalidade, número de requisitos funcionais e seções de fora de escopo.

## Princípios Fundamentais
- Esclareça antes de planejar; planeje antes de redigir.
- Minimize ambiguidade — prefira declarações mensuráveis e verificáveis.
- O PRD define resultados e restrições, NÃO a implementação.
- Sempre considere usabilidade e acessibilidade.
- Cada requisito deve ser rastreável e testável.
- O documento deve ser estável — evite detalhes que mudam frequentemente.

## Lista de Verificação de Qualidade
- [ ] Documentos de contexto (vision, product_map, roadmap) consultados.
- [ ] Perguntas de esclarecimento concluídas e respondidas.
- [ ] Plano seção por seção apresentado e aprovado pelo usuário.
- [ ] Template seguido sem desvios.
- [ ] Requisitos funcionais numerados e verificáveis.
- [ ] User stories no formato padrão.
- [ ] Sem detalhes de implementação ou decisões de arquitetura.
- [ ] Fora de escopo documentado.
- [ ] Documento com menos de 2000 palavras.
- [ ] Arquivo salvo em `./ai-sdd/prd-[feature-slug]/prd.md`.

## Tratamento de Erros
- Se o usuário fornecer contexto insuficiente, faça perguntas de esclarecimento complementares antes de prosseguir.
- Se o arquivo do template estiver faltando, relate o erro e interrompa — não gere um PRD sem o template.
- Se o diretório de saída já existir com um PRD, confirme com o usuário antes de sobrescrever.
- Se o plano for rejeitado pelo usuário, revise com base no feedback e reapresente para aprovação.
