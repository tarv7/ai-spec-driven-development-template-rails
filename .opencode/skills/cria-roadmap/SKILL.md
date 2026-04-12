---
name: cria-roadmap
description: Cria o documento roadmap.md organizando a ordem de implementação das funcionalidades em fases incrementais com base no product_map e na vision. Define prioridades, dependências e critérios de priorização. Use após criar o product_map. Não use para descrever fluxos de usuário, requisitos detalhados ou especificações técnicas.
---

# Criação de Roadmap

## Procedimentos

**Passo 1: Validar Pré-requisitos**
1. Verifique se existe `./ai-sdd/system/product_map.md`.
2. Se não existir, interrompa e direcione o usuário para a skill `cria-product-map`.
3. Verifique se existe `./ai-sdd/system/vision.md` (contexto adicional).
4. Leia ambos os documentos para extrair: fluxos mapeados, tipos de usuário, escopo e proposta de valor.

**Passo 2: Esclarecer Prioridades (Obrigatório)**
1. Faça perguntas de esclarecimento ao usuário antes de gerar qualquer conteúdo:
   - **Prioridade de Valor**: Qual aspecto do sistema entrega mais valor primeiro? (ex: área pública vs administrativa)
   - **Restrições de Prazo**: Existe um deadline ou MVP definido?
   - **Dependências Externas**: Existem integrações ou dependências que impactam a ordem? (ex: API de terceiros, aprovações)
   - **Paralelismo**: É possível trabalhar em fases simultaneamente ou a execução é estritamente sequencial?
   - **Critérios de Sucesso**: Como o usuário mede o sucesso de cada fase? (ex: deploy, validação com cliente, métrica)
2. NÃO prossiga até obter respostas suficientes para definir as fases.

**Passo 3: Identificar Blocos do Sistema (Obrigatório)**
1. A partir do product_map, agrupe as funcionalidades em categorias lógicas:
   - **Fundação**: Infraestrutura, autenticação, configurações base.
   - **Domínio**: Modelos de dados, relacionamentos, estrutura central.
   - **Fluxos Principais**: Funcionalidades core que entregam valor ao usuário final.
   - **Funcionalidades Complementares**: Melhorias, otimizações, funcionalidades secundárias.
2. Identifique dependências entre os blocos (o que precisa existir antes do quê).

**Passo 4: Planejar Fases (Obrigatório)**
1. Organize os blocos em fases incrementais, onde cada fase:
   - Tem um objetivo claro e mensurável.
   - Entrega valor funcional (não apenas infraestrutura isolada, quando possível).
   - Respeita as dependências — fundação antes de features.
2. Limite a 4-6 fases (agrupe conforme necessário para evitar granularidade excessiva).
3. Para cada fase, liste:
   - **Objetivos**: O que essa fase alcança.
   - **Entregas**: Itens concretos e verificáveis.
4. Apresente o plano de fases ao usuário para alinhamento.
5. Aguarde aprovação do usuário antes de prosseguir para o Passo 5.

**Passo 5: Redigir Documento (Obrigatório)**
1. Estrutura do documento:
   - **Visão Geral**: Resumo de uma linha do roadmap (número de fases, abordagem).
   - **Fases Numeradas**: Para cada fase — objetivos e entregas.
   - **Ordem de Dependências**: Diagrama textual mostrando o fluxo entre fases e possíveis paralelismos.
   - **Critérios de Priorização**: Lista dos critérios usados para ordenar as fases.
   - **Estimativa de Esforço**: Tabela com fase, complexidade e estimativa relativa.
2. Regras de redação:
   - Entregas devem ser concretas e verificáveis (não vagas).
   - NÃO inclua detalhes de implementação (código, queries, APIs) — isso pertence à tech spec.
   - NÃO repita descrições de fluxos do product_map — referencie os fluxos pelo nome.
   - Linguagem orientada a entregas, não a tarefas técnicas.
3. Mantenha o documento conciso — foque em clareza e acionabilidade.

**Passo 6: Salvar Arquivo**
1. Crie o diretório `./ai-sdd/system/` se não existir.
2. Caminho: `./ai-sdd/system/roadmap.md`.
3. Se o arquivo já existir, confirme com o usuário antes de sobrescrever.

**Passo 7: Relatar Resultado**
1. Informe o caminho final do arquivo.
2. Resuma: número de fases, fases com possibilidade de paralelismo e estimativa total.

## Princípios Fundamentais
- Esclareça antes de planejar; planeje antes de redigir.
- Priorizar dependências — fundação antes de features.
- Cada fase deve entregar valor incremental e verificável.
- Evitar fases muito grandes (máx. 6-8 entregas por fase).
- O roadmap define O QUE entregar e QUANDO, não COMO implementar.
- O documento deve ser estável — evite detalhes que mudam frequentemente.

## Lista de Verificação de Qualidade
- [ ] Product map e vision analisados.
- [ ] Perguntas de esclarecimento concluídas e respondidas.
- [ ] Blocos do sistema identificados e agrupados.
- [ ] Plano de fases apresentado e aprovado pelo usuário.
- [ ] Fases com objetivos claros e entregas concretas.
- [ ] Dependências entre fases documentadas.
- [ ] Critérios de priorização explícitos.
- [ ] Sem detalhes de implementação.
- [ ] Arquivo salvo em `./ai-sdd/system/roadmap.md`.

## Tratamento de Erros
- Se `product_map.md` não existir, interrompa e direcione o usuário para a skill `cria-product-map`.
- Se o usuário fornecer contexto insuficiente sobre prioridades, faça perguntas de esclarecimento complementares.
- Se o plano de fases for rejeitado, revise com base no feedback e reapresente para aprovação.
- Se o arquivo de saída já existir, confirme com o usuário antes de sobrescrever.
