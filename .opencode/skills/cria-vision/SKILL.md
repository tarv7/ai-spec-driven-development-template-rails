---
name: cria-vision
description: Cria o documento de visão do produto (vision.md) a partir de uma ideia ou problema. Define propósito, público-alvo e proposta de valor do sistema. Use no início de um projeto ou quando houver necessidade de redefinir a direção do produto. Não use para definir funcionalidades detalhadas ou requisitos técnicos.
---

# Criação de Vision

## Procedimentos

**Passo 1: Validar Entrada**
1. Confirme que o usuário forneceu uma ideia, problema ou descrição do produto.
2. Caso esteja vago, solicite mais contexto antes de prosseguir.

**Passo 2: Esclarecer Contexto (Obrigatório)**
1. Faça perguntas de esclarecimento ao usuário antes de gerar qualquer conteúdo:
   - **Problema**: Qual problema o produto resolve? Para quem é doloroso?
   - **Público-Alvo**: Quem são os usuários principais? Há usuários secundários?
   - **Proposta de Valor**: Por que esse produto é melhor/diferente das alternativas?
   - **Contexto de Uso**: Em que situação e ambiente será usado?
   - **Restrições**: Existem limitações de prazo, orçamento ou tecnologia relevantes?
2. NÃO prossiga até obter respostas suficientes para redigir o documento.

**Passo 3: Planejar Documento (Obrigatório)**
1. Defina estrutura do documento:
   - Problema
   - Solução
   - Público-alvo
   - Proposta de valor
   - Escopo geral (alto nível, sem funcionalidades detalhadas)
2. Liste suposições e premissas.
3. Apresente o plano ao usuário para alinhamento antes de redigir.

**Passo 4: Redigir Vision (Obrigatório)**
1. Criar documento conciso (máx. ~500 palavras).
2. NÃO listar funcionalidades detalhadas.
3. Focar em clareza estratégica — o documento deve ser estável ao longo do tempo.
4. Usar linguagem acessível, evitando jargão técnico desnecessário.

**Passo 5: Salvar Arquivo**
1. Crie o diretório `./ai-sdd/system/` se não existir.
2. Caminho: `./ai-sdd/system/vision.md`

**Passo 6: Relatar Resultado**
1. Informar caminho final do arquivo.
2. Resumo breve do conteúdo gerado.

## Princípios Fundamentais
- Esclareça antes de planejar; planeje antes de redigir.
- Foco em propósito, não features.
- Clareza > completude.
- O documento deve ser estável ao longo do tempo — evite detalhes que mudam frequentemente.

## Lista de Verificação de Qualidade
- [ ] Perguntas de esclarecimento concluídas e respondidas.
- [ ] Plano apresentado ao usuário para alinhamento.
- [ ] Problema claro e específico definido.
- [ ] Público-alvo identificado com clareza.
- [ ] Proposta de valor clara e diferenciada.
- [ ] Sem detalhes técnicos ou funcionalidades granulares.
- [ ] Linguagem acessível e concisa.
- [ ] Arquivo salvo em `./ai-sdd/system/vision.md`.

## Tratamento de Erros
- Se o usuário fornecer contexto insuficiente, faça perguntas de esclarecimento complementares antes de prosseguir.
- Se o diretório de saída já contiver um `vision.md`, confirme com o usuário antes de sobrescrever.
- Se o plano for rejeitado pelo usuário, revise com base no feedback e reapresente para alinhamento.
