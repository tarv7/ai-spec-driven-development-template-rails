---
name: executa-review
description: Realiza revisão de código de uma funcionalidade completa (PRD), verificando todas as tarefas, aderência à Tech Spec, qualidade de código e testes. Publica comentários inline nas linhas problemáticas e um parecer final resumido diretamente no Pull Request via GitHub MCP. Use quando o usuário fornecer um número de PR para revisão. Não use para testes de QA, correção de bugs ou implementação de tarefas.
---

# Execução de Revisão de Código

## Pré-requisitos
- O GitHub MCP deve estar disponível (`mcp_github_*`).
- O usuário deve fornecer o número do PR.

## Procedimentos

**Passo 1: Identificar PR e Contexto (Obrigatório)**
1. Use o número do PR fornecido pelo usuário.
2. Use `mcp_github_get_pull_request` para obter detalhes: `owner`, `repo`, `pull_number`, branch base, branch head, descrição.
3. Identifique o slug da funcionalidade a partir do título/descrição do PR ou pergunte ao usuário.
4. Se o PR não existir, informe o usuário e interrompa.

**Passo 2: Carregar Documentação do PRD (Obrigatório)**
1. Leia o PRD em `./ai-sdd/prd-[feature-slug]/prd.md` — requisitos funcionais (RF-XXX).
2. Leia a Tech Spec em `./ai-sdd/prd-[feature-slug]/techspec.md` — decisões arquiteturais.
3. Leia as Tasks em `./ai-sdd/prd-[feature-slug]/tasks.md` — escopo e status das tarefas.
4. Leia `AGENTS.md` para conhecer os padrões do projeto.
5. NÃO pule este passo — o review é baseado na entrega do PRD.

**Passo 3: Analisar Alterações de Código (Obrigatório)**
1. Use `mcp_github_get_pull_request_files` para obter a lista de arquivos alterados com seus diffs.
2. Para cada arquivo modificado:
   a. Leia o **contexto completo do arquivo**, não apenas o diff.
   b. Analise as alterações linha por linha.
   c. Registre problemas encontrados com: arquivo, posição no diff (`position`), descrição e sugestão.
3. Verifique se há arquivos que DEVERIAM ter sido modificados (conforme Tech Spec/Tasks) mas não foram.

**Passo 4: Verificação de Completude das Tarefas (Obrigatório)**
1. Para cada tarefa do PRD marcada como concluída em `tasks.md`:
   - O código correspondente foi implementado no PR.
   - Os requisitos funcionais (RF-XXX) referenciados na tarefa foram atendidos.
   - Os critérios de sucesso da tarefa foram atingidos.
2. Para tarefas NÃO marcadas como concluídas:
   - Verifique se o PR deveria incluí-las ou se são de outro escopo.

**Passo 5: Verificação de Aderência à Tech Spec (Obrigatório)**
1. Compare a implementação com a Tech Spec:
   - Arquitetura implementada conforme especificado.
   - Componentes criados/modificados conforme definido.
   - Modelos de dados e migrações conforme documentado.
   - Rotas e controllers conforme especificado.
   - Integrações implementadas corretamente.
2. Registre desvios como problemas — indicando o que a Tech Spec esperava vs. o que foi implementado.

**Passo 6: Inspeção de Qualidade de Código (Obrigatório)**
1. Leia `references/code-quality-checklist.md` e aplique cada categoria:
   - **Design e Estrutura**: Complexidade, DRY, SOLID, acoplamento.
   - **Segurança**: SQL injection, XSS, mass assignment, autenticação, dados sensíveis, etc.
   - **Performance**: N+1 queries, índices, queries pesadas, caching.
   - **Testes**: Cobertura, significância, edge cases, isolamento.
2. Cada item com prioridade 🔴 Alta que falhar DEVE ser reportado como problema.
3. Itens 🟡 Média são recomendações. Itens 🟢 Baixa são sugestões opcionais.

**Passo 7: Execução dos Testes (Obrigatório)**
1. Execute a suíte de testes: `bin/rails test`.
2. Execute lint e segurança: `bin/rubocop -a -S -s`.
3. Verifique:
   - Todos os testes passam.
   - Novos testes foram adicionados para código novo.
   - A cobertura não diminuiu.
   - Os testes são significativos (validam comportamento de negócio).
4. Se qualquer teste falhar, o review DEVE ser `REQUEST_CHANGES` — independentemente dos demais achados.

**Passo 8: Publicar Comentários Inline no PR (Obrigatório)**
1. Para cada problema encontrado nos passos anteriores:
   - Use `mcp_github_create_pull_request_review_comment` com:
     - `path`: caminho relativo do arquivo.
     - `position`: posição no diff unificado (linha do diff, não do arquivo).
     - `body`: comentário em markdown com:
       - Prefixo de severidade: `🔴 Alta`, `🟡 Média` ou `🟢 Baixa`.
       - Descrição clara do problema.
       - Sugestão concreta de correção.
       - Referência ao checklist, AGENTS.md, Tech Spec ou RF-XXX quando aplicável.
2. Agrupe comentários relacionados no mesmo arquivo para evitar ruído.
3. Seja construtivo — sempre ofereça alternativas, nunca apenas critique.

**Passo 9: Submeter Parecer Final no PR (Obrigatório)**
1. Determine o `event` do review:
   - `APPROVE` → **APROVADO**: todos os requisitos atendidos, testes passando, código conforme padrões.
   - `COMMENT` → **APROVADO COM OBSERVAÇÕES**: critérios principais atendidos, apenas problemas menores não bloqueantes.
   - `REQUEST_CHANGES` → **REPROVADO**: testes falhando, violações 🔴 Alta, não aderência à Tech Spec ou problemas de segurança.
2. Use `mcp_github_create_pull_request_review` com:
   - `owner`, `repo`, `pull_number` obtidos no Passo 1.
   - `event`: `APPROVE`, `COMMENT` ou `REQUEST_CHANGES`.
   - `body`: **resumo conciso** (não o relatório completo) contendo:
     - Parecer (Aprovado / Aprovado com observações / Reprovado).
     - Total de problemas por severidade (🔴 X / 🟡 Y / 🟢 Z).
     - Pontos positivos observados (1-2 itens).
     - Ações necessárias (se reprovado).

## Princípios Fundamentais
- O review é baseado na entrega de um PRD — verificar completude, não apenas estilo.
- Comentários inline são a forma primária de feedback — cada problema na linha exata.
- O parecer final é um resumo, não um relatório extenso.
- Seja construtivo — sugira correções, não apenas aponte problemas.
- Testes falhando = reprovação automática.
- Prioridade 🔴 Alta no checklist = bloqueante para aprovação.

## Lista de Verificação de Qualidade
- [ ] PR identificado e detalhes obtidos via GitHub MCP.
- [ ] PRD, Tech Spec e Tasks lidos completamente.
- [ ] AGENTS.md consultado.
- [ ] Todos os arquivos alterados analisados com contexto completo.
- [ ] Completude das tarefas verificada contra `tasks.md`.
- [ ] Aderência à Tech Spec verificada.
- [ ] Checklist de qualidade aplicado (`references/code-quality-checklist.md`).
- [ ] `bin/rails test` executado — todos passando.
- [ ] `bin/rubocop -a -S -s` executado — sem erros.
- [ ] Comentários inline publicados no PR para cada problema.
- [ ] Parecer final resumido submetido no PR.

## Tratamento de Erros
- Se o número do PR não for fornecido, pergunte ao usuário.
- Se o PR não existir, informe e interrompa.
- Se o PRD/Tech Spec/Tasks não existirem, informe e pergunte se deve prosseguir apenas com o checklist de qualidade.
- Se os testes falharem, o `event` DEVE ser `REQUEST_CHANGES` independentemente dos demais achados.
- Se o GitHub MCP não estiver disponível, gere o relatório localmente e informe o usuário.
- Seja construtivo nas críticas — sempre sugira alternativas nos comentários inline.
