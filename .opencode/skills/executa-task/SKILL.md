---
name: executa-task
description: Implementa tarefas de funcionalidades lendo o contexto do PRD/TechSpec, analisando dependências, executando a implementação com testes e verificações de qualidade. Marca as tarefas como concluídas em tasks.md e aciona revisão ao finalizar. Use quando o usuário solicitar implementar uma tarefa, executar uma tarefa ou começar a trabalhar em um número de tarefa específico. Não use para criar tarefas, rodar QA, revisão de código ou correção de bugs avulsos.
---

# Execução de Tarefas

## Procedimentos

**Passo 1: Configuração Pré-Tarefa (Obrigatório)**
1. Confirme que o slug da funcionalidade e o número da tarefa foram fornecidos.
2. Leia o arquivo da tarefa em `./ai-sdd/prd-[feature-slug]/tasks/[num]_task.md`.
3. Leia o PRD em `./ai-sdd/prd-[feature-slug]/prd.md` para contexto de negócio.
4. Leia a Tech Spec em `./ai-sdd/prd-[feature-slug]/techspec.md` para decisões técnicas.
5. Identifique dependências de tarefas anteriores e verifique se estão concluídas em `tasks.md`.
6. **[OPCIONAL — Paper]** Verifique se existe `./ai-sdd/prd-[feature-slug]/paper-artboards.md`.
   - Se existir E a task tiver referências de design (seção "Design de Referência (Paper)"), marque `usa_paper = true`.
   - Se não existir ou a task não referenciar artboards, marque `usa_paper = false` e prossiga normalmente.
7. NÃO pule nenhuma dessas leituras — a tarefa será invalidada sem contexto completo.

**Passo 2: Carregar Skills e Padrões (Obrigatório)**
1. Leia `AGENTS.md` para reforçar convenções, comandos e padrões do projeto.
2. Identifique skills em `.opencode/skills/` relevantes para as tecnologias da tarefa.
   - Se a task tem UI: sempre carregue `rails-visual-design` e `rails-components`.
   - **[OPCIONAL — Paper]** Se `usa_paper = true`: carregue também `paper-to-rails`.
3. Consulte documentação de frameworks e bibliotecas envolvidas quando necessário (use Context7 MCP).

**Passo 3: Análise da Tarefa (Obrigatório)**
1. Analise a tarefa identificando:
   - **Objetivo**: O que esta tarefa entrega e por quê.
   - **Requisitos**: Quais RF-XXX do PRD são atendidos.
   - **Decisões técnicas**: O que a Tech Spec define para esta área.
   - **Dependências**: Tarefas anteriores e código existente necessário.
   - **Arquivos**: Quais arquivos serão criados ou modificados.
   - **Riscos**: Pontos de atenção e possíveis complicações.
2. Gere um resumo conciso antes de iniciar a implementação.

**Passo 4: Plano de Abordagem (Obrigatório)**
1. Defina uma abordagem numerada passo a passo com base na análise.
2. Cada passo deve ser concreto e verificável.
3. Inclua ordem de implementação respeitando dependências internas.
4. Inicie a implementação imediatamente após definir o plano — NÃO aguarde aprovação.

**Passo 5: Implementação (Obrigatório)**
1. Implemente seguindo o plano definido no Passo 4.
2. Regras de implementação:
   - Siga todos os padrões do `AGENTS.md` (convenções Rails, nomenclatura, i18n, etc.).
   - Todas as strings visíveis ao usuário devem usar i18n (`t('.chave')` nas views).
   - Implemente soluções de **causa raiz** — sem workarounds.
   - Prefira bibliotecas existentes ao invés de código customizado.
   - Adicione índices em colunas frequentemente consultadas.
   - Use strong parameters em controllers.
   - Use `ApplicationRecord`, `ApplicationController`, `ApplicationJob` como base.
3. **[OPCIONAL — Paper]** Se `usa_paper = true`: execute a skill `paper-to-rails` para gerar as views.
   - A skill tentará conectar ao Paper MCP. Se falhar, informe o usuário e gere views via `rails-visual-design`.
   - Se `usa_paper = false`: gere views seguindo `rails-visual-design` + `rails-components`.
4. Siga a Tech Spec para decisões de arquitetura e design.
5. Referencie requisitos do PRD (RF-XXX) nos comentários quando relevante.

**Passo 6: Testes (Obrigatório)**
1. Crie testes para toda funcionalidade implementada:
   - **Unitários** (Minitest): Modelos, validações, scopes, métodos de negócio.
   - **Integração**: Controllers, strong parameters, respostas, redirecionamentos.
   - **E2E** (Capybara): Fluxos do usuário quando aplicável.
2. Use fixtures para dados de teste em `test/fixtures/`.
   - As fixtures precisam representar dados reais do sistema para melhor storytelling.
3. Execute todos os testes: `bin/rails test`.
4. Todos os testes devem passar — corrija falhas antes de prosseguir.

**Passo 7: Verificações Automatizadas (Obrigatório)**
1. Execute lint e segurança: `bin/rubocop -a -S -s`.
2. Corrija todos os problemas apontados pelo RuboCop.
3. Re-execute os testes após correções: `bin/rails test`.

**Passo 8: Marcar Tarefa como Concluída (Obrigatório)**
1. Após implementação, testes passando e RuboCop limpo:
   - Atualize o status da tarefa em `./ai-sdd/prd-[feature-slug]/tasks.md` (⬜ → ✅).
   - Marque subtarefas concluídas na tarefa individual.

**Passo 9: Relatar Resultado**
1. Resuma o que foi implementado:
   - Arquivos criados/modificados.
   - Testes criados e resultado da execução.
   - Requisitos do PRD atendidos (RF-XXX).
   - Resultado do RuboCop.
2. Informe que a tarefa está pronta para revisão via skill `executa-review`.

## Princípios Fundamentais
- Contexto completo antes de implementar — leia PRD, Tech Spec e tarefa.
- Soluções de causa raiz, nunca workarounds.
- Testes são parte integrante da implementação, não uma fase posterior.
- Siga os padrões do projeto (`AGENTS.md`) rigorosamente.
- Código limpo e verificado (RuboCop + testes) antes de marcar como concluído.
- A implementação deve ser fiel à Tech Spec e atender os requisitos do PRD.

## Lista de Verificação de Qualidade
- [ ] PRD, Tech Spec e arquivo da tarefa lidos completamente.
- [ ] Dependências de tarefas anteriores verificadas.
- [ ] Skills e AGENTS.md consultados.
- [ ] `paper-artboards.md` verificado (detectar se tarefa usa Paper).
- [ ] Plano de abordagem definido.
- [ ] Implementação segue padrões Rails (convenções, i18n, strong params).
- [ ] Implementação segue a Tech Spec.
- [ ] Views geradas via `paper-to-rails` (se Paper disponível) ou `rails-visual-design` (fallback).
- [ ] Testes unitários criados e passando.
- [ ] Testes de integração criados e passando.
- [ ] Testes E2E criados quando aplicável.
- [ ] `bin/rubocop -a -S -s` executado sem erros.
- [ ] `bin/rails test` executado sem falhas.
- [ ] Tarefa marcada como concluída em `tasks.md`.
- [ ] Resultado relatado ao usuário.

## Tratamento de Erros
- Se o arquivo da tarefa não existir, interrompa e informe o usuário.
- Se o PRD ou Tech Spec estiver ausente, interrompa e direcione para a skill correta.
- Se as dependências não estiverem concluídas, avise o usuário e pergunte se deseja prosseguir.
- Se os testes falharem, corrija os problemas antes de marcar a tarefa como concluída.
- Se o RuboCop reportar erros, corrija-os antes de finalizar.
- Se surgir ambiguidade na tarefa, consulte o PRD e Tech Spec — se ainda ambíguo, pergunte ao usuário.
