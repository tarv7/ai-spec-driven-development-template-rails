---
name: cria-tasks
description: Converte PRD e Tech Spec em uma lista detalhada e sequencial de tarefas de implementação. Cada tarefa é um entregável funcional e incremental com sua própria suíte de testes. Gera tasks.md (resumo) e arquivos de tarefas individuais com subtarefas, critérios de sucesso e testes. Use quando o usuário solicitar criar tarefas, decompor trabalho ou planejar implementação a partir de PRD e Tech Spec existentes. Não use para criação de PRD, criação de tech spec ou implementação real de código.
---

# Criação de Tarefas

## Procedimentos

**Passo 1: Validar Pré-requisitos**
1. Confirme que o slug da funcionalidade foi fornecido.
2. Verifique se o PRD existe em `ai-sdd/prd-[feature-slug]/prd.md`. Se ausente, interrompa e direcione o usuário para a skill `cria-prd`.
3. Verifique se o Tech Spec existe em `ai-sdd/prd-[feature-slug]/techspec.md`. Se ausente, interrompa e direcione o usuário para a skill `cria-techspec`.

**Passo 2: Analisar PRD e Tech Spec (Obrigatório)**
1. Leia o PRD completamente para extrair:
   - Requisitos funcionais (RF-XXX).
   - Objetivos e métricas de sucesso.
   - Histórias de usuário e fluxos.
   - Fora de escopo.
2. Leia o Tech Spec completamente para extrair:
   - Componentes e suas responsabilidades.
   - Modelos de dados e migrações.
   - Rotas e endpoints.
   - Sequenciamento de desenvolvimento sugerido.
   - Dependências técnicas.
3. Identifique o grafo de dependências entre componentes.

**Passo 3: Gerar Lista de Tarefas de Alto Nível (Obrigatório)**
1. Apresente a lista ao usuário para aprovação ANTES de gerar qualquer arquivo.
2. Regras de decomposição:
   - Organize tarefas por **entregável lógico** (não por tipo técnico).
   - Ordene respeitando dependências: fundação → backend → frontend → integração.
   - Cada tarefa DEVE ser um **entregável funcional e incremental** — completável independentemente.
   - Cada tarefa DEVE incluir **testes** como parte integrante (não como tarefa separada).
   - Referencie os requisitos do PRD (RF-XXX) que cada tarefa atende.
3. Limites:
   - Máximo de **15 tarefas** principais (agrupe conforme necessário).
   - Use formato **X.0** para tarefas principais.
4. Aguarde aprovação do usuário antes de prosseguir para o Passo 4.

**Passo 4: Gerar Arquivos de Tarefas (Obrigatório)**
1. Leia o template de resumo em `assets/tasks-template.md`.
2. Leia o template de tarefa individual em `assets/task-template.md`.
3. Crie o arquivo de resumo: `./ai-sdd/prd-[feature-slug]/tasks.md`.
4. Crie arquivos de tarefas individuais: `./ai-sdd/prd-[feature-slug]/tasks/[num]_task.md`.
5. Regras de preenchimento:
   - **Numeração**: X.0 para tarefas, X.Y para subtarefas.
   - **Subtarefas**: Decomponha cada tarefa em passos acionáveis e concretos.
   - **Referências**: NÃO repita detalhes de implementação da Tech Spec — referencie-a.
   - **Testes**: Inclua testes como subtarefas dentro de cada tarefa (unit, integração, E2E quando aplicável).
   - **Critérios de Sucesso**: Cada tarefa deve ter resultados mensuráveis e verificáveis.
   - **Arquivos**: Liste arquivos que serão criados ou modificados.
6. Se os diretórios de saída já contiverem arquivos, confirme com o usuário antes de sobrescrever.

**Passo 5: Relatar Resultados**
1. Apresente todos os arquivos gerados ao usuário.
2. Resuma: total de tarefas, total de subtarefas e requisitos do PRD cobertos.
3. NÃO inicie implementação — aguarde que o usuário acione a skill `executa-task`.

## Princípios Fundamentais
- Assuma que o leitor principal é um **desenvolvedor júnior** — seja o mais claro possível.
- Cada tarefa deve ser completável independentemente e entregar valor funcional.
- Testes são parte integrante de cada tarefa, não uma fase separada.
- Referencie PRD e Tech Spec — não copie conteúdo deles.
- NÃO implemente nada — foque somente em listar e detalhar tarefas.
- Ordem de execução deve respeitar o grafo de dependências.

## Lista de Verificação de Qualidade
- [ ] PRD e Tech Spec lidos e analisados completamente.
- [ ] Requisitos funcionais (RF-XXX) mapeados para tarefas.
- [ ] Lista de tarefas de alto nível aprovada pelo usuário.
- [ ] Arquivos de tarefas gerados usando templates.
- [ ] Cada tarefa tem subtarefas claras e acionáveis.
- [ ] Cada tarefa tem testes (unit, integração e/ou E2E).
- [ ] Cada tarefa tem critérios de sucesso mensuráveis.
- [ ] Cada tarefa lista arquivos relevantes.
- [ ] Dependências entre tarefas respeitadas na ordenação.
- [ ] Sem detalhes de implementação copiados da Tech Spec.
- [ ] Arquivos salvos em `./ai-sdd/prd-[feature-slug]/`.
- [ ] Resultados apresentados ao usuário.

## Tratamento de Erros
- Se o PRD estiver ausente, interrompa e direcione o usuário para a skill `cria-prd`.
- Se o Tech Spec estiver ausente, interrompa e direcione o usuário para a skill `cria-techspec`.
- Se o usuário rejeitar a lista de alto nível, revise com base no feedback e reapresente para aprovação.
- Se o diretório de saída já contiver arquivos de tarefas, confirme com o usuário antes de sobrescrever.
- Se uma tarefa cobrir requisitos demais, subdivida em tarefas menores e mais focadas.
