---
name: cria-product-map
description: Cria o documento product_map.md descrevendo os principais fluxos do sistema do ponto de vista do usuário. Mapeia jornadas completas (começo → meio → fim) organizadas por tipo de usuário. Use após definir a visão do produto (vision.md). Não use para planejamento técnico, implementação ou definição de requisitos detalhados.
---

# Criação de Product Map

## Procedimentos

**Passo 1: Validar Pré-requisitos**
1. Verifique se existe `./ai-sdd/system/vision.md`.
2. Se não existir, interrompa e direcione o usuário para a skill `cria-vision`.
3. Leia o documento de visão para extrair: problema, público-alvo, proposta de valor e escopo.

**Passo 2: Esclarecer Fluxos (Obrigatório)**
1. Faça perguntas de esclarecimento ao usuário antes de gerar qualquer conteúdo:
   - **Tipos de Usuário**: Quais são os principais perfis que interagem com o sistema? (ex: visitante, admin, operador)
   - **Ações Principais**: Para cada tipo de usuário, quais são as ações mais importantes que ele realiza?
   - **Fluxos Críticos**: Quais jornadas são essenciais para o produto funcionar? (ex: cadastro, compra, busca)
   - **Restrições de Acesso**: Existem diferenças de permissão entre tipos de usuário?
   - **Pontos de Conversão**: Onde o sistema captura valor? (ex: geração de lead, checkout, assinatura)
2. NÃO prossiga até obter respostas suficientes para mapear os fluxos.

**Passo 3: Planejar Fluxos (Obrigatório)**
1. Organize os fluxos identificados por tipo de usuário.
2. Para cada tipo de usuário, liste os fluxos principais com nome descritivo.
3. Apresente o plano ao usuário para alinhamento antes de redigir.
4. Aguarde aprovação do usuário antes de prosseguir para o Passo 4.

**Passo 4: Redigir Documento (Obrigatório)**
1. Estrutura do documento:
   - **Visão Geral**: Resumo de uma linha do sistema (derivado da vision.md).
   - **Tipos de Usuário**: Lista numerada dos perfis identificados.
   - **Fluxos por Tipo de Usuário**: Seção separada para cada tipo, com fluxos numerados sequencialmente.
2. Para cada fluxo:
   - Nomeio descritivo e orientado à ação (ex: "Buscar Produtos", "Gerenciar Catálogo").
   - Passos numerados descrevendo a jornada completa (começo → meio → fim).
   - Linguagem simples e orientada ao comportamento visível ao usuário.
   - NÃO inclua detalhes de implementação, banco de dados, APIs ou tecnologias.
3. Inclua seção de **Observações** para restrições, premissas e decisões relevantes.
4. Mantenha o documento conciso — foque em clareza, não em volume.

**Passo 5: Salvar Arquivo**
1. Crie o diretório `./ai-sdd/system/` se não existir.
2. Caminho: `./ai-sdd/system/product_map.md`.
3. Se o arquivo já existir, confirme com o usuário antes de sobrescrever.

**Passo 6: Relatar Resultado**
1. Informe o caminho final do arquivo.
2. Resuma: quantidade de tipos de usuário e total de fluxos mapeados.

## Princípios Fundamentais
- Esclareça antes de planejar; planeje antes de redigir.
- Descrever comportamento visível, nunca implementação.
- Cada fluxo deve ter começo, meio e fim — sem passos soltos.
- Linguagem simples e orientada ao usuário final.
- O document deve ser estável — evite detalhes que mudam frequentemente.

## Lista de Verificação de Qualidade
- [ ] Perguntas de esclarecimento concluídas e respondidas.
- [ ] Plano de fluxos apresentado e aprovado pelo usuário.
- [ ] Tipos de usuário claramente identificados.
- [ ] Fluxos principais mapeados com passos sequenciais.
- [ ] Sem detalhes técnicos ou de implementação.
- [ ] Observações e premissas documentadas.
- [ ] Arquivo salvo em `./ai-sdd/system/product_map.md`.

## Tratamento de Erros
- Se `vision.md` não existir, interrompa e direcione o usuário para a skill `cria-vision`.
- Se o usuário fornecer contexto insuficiente, faça perguntas de esclarecimento complementares.
- Se o plano de fluxos for rejeitado, revise com base no feedback e reapresente para aprovação.
- Se o arquivo de saída já existir, confirme com o usuário antes de sobrescrever.
