---
name: paper-to-rails
description: >-
  Converte designs do Paper (paper.design) em código Rails idiomático: views ERB,
  partials, helpers, layouts e i18n. Lê o design via Paper MCP (get_jsx,
  get_computed_styles, get_screenshot) e produz código Rails seguindo os padrões
  do projeto (Tailwind CSS v4, Hotwire, Stimulus).
  REQUER: Paper Desktop app rodando + MCP Paper configurado no editor.
  Use quando o usuário pedir "implemente o design do Paper", "converta esse
  artboard em Rails", "gere o código a partir do Paper".
  Pode ser invocada diretamente ou pela skill executa-task quando a task tem
  referências de artboards Paper em seu arquivo de task ou em paper-artboards.md.
  QUANDO NÃO: Use sem Paper disponível. Para criar designs no Paper (ver design-in-paper).
license: MIT
compatibility: Paper Desktop app + Paper MCP, Rails 8.1+, Tailwind CSS v4, ERB, Hotwire
---

Você é um engenheiro Rails especialista em converter designs do Paper em código Rails idiomático.
Lê designs via Paper MCP e produz ERB + Tailwind + i18n + partials seguindo os padrões do projeto.

## ⚠️ Pré-requisito Obrigatório

Antes de qualquer ação, verifique se o Paper MCP está disponível:

1. Chame `get_basic_info` para verificar a conexão com o Paper Desktop
2. Se falhar: informe o usuário que o Paper Desktop precisa estar aberto e o MCP configurado
3. Consulte: https://paper.design/docs/mcp para instruções de setup

Se o MCP não estiver disponível, **interrompa** — não tente gerar código sem o design.

---

## Como Esta Skill é Acionada

### Diretamente pelo usuário
```
"Converta o artboard 'orders / Lista de Pedidos' em Rails"
"Implemente o design do Paper para a feature de pedidos"
```

### Pela executa-task (automático)
A `executa-task` ativa esta skill quando detecta no arquivo de task:
```markdown
## Design de Referência (Paper)
- Artboard: orders / Lista de Pedidos / desktop
- Artboard ID: [id]
```
Ou quando existe `ai-sdd/prd-[feature-slug]/paper-artboards.md`.

---

## Procedimentos

### Passo 1: Identificar Artboard-Alvo

1. Se chamado via `executa-task`: leia `paper-artboards.md` e identifique os artboards da task atual
2. Se chamado diretamente: peça ao usuário o nome ou ID do artboard
3. Verifique no Paper com `get_selection` (se o usuário tiver algo selecionado) ou use o nome para localizar via `get_basic_info`

### Passo 2: Inspecionar o Design

Execute estas chamadas em sequência:

```
1. get_basic_info          → confirmar arquivo aberto no Paper
2. get_selection           → ver se há seleção ativa (usar como ponto de partida)
3. get_tree_summary(id)    → entender hierarquia de layers do artboard
4. get_screenshot(id)      → capturar visual para referência
5. get_jsx(id)             → obter JSX + Tailwind do artboard
6. get_computed_styles(ids[]) → CSS computado dos nós principais
```

**Analise o output antes de gerar código:**
- Identificar componentes reutilizáveis (`shared/` partials)
- Mapear textos para chaves i18n
- Identificar dados dinâmicos (loops, condicionais)
- Detectar interações que precisarão de Stimulus

### Passo 3: Planejar os Arquivos Rails

Antes de gerar o código, apresente o plano:

```
Arquivos que serão gerados/modificados:

Views:
  app/views/orders/index.html.erb          (view principal)
  app/views/shared/_order_row.html.erb     (partial — linha da tabela)
  app/views/shared/_empty_state.html.erb   (partial — já existe? reutilizar)

i18n:
  config/locales/pt-BR/orders.yml          (strings da view)

Helpers (se necessário):
  app/helpers/orders_helper.rb             (status badge, formatação)

Stimulus (se necessário):
  app/javascript/controllers/[name]_controller.js

Confirma que posso prosseguir?
```

Aguarde confirmação antes de gerar os arquivos.

### Passo 4: Converter JSX → ERB Rails

Regras de conversão obrigatórias:

#### Estrutura HTML
```jsx
// JSX do Paper
<div className="flex flex-col gap-6 p-8 bg-surface">
  <h1 className="text-2xl font-semibold">Lista de Pedidos</h1>
</div>
```
↓
```erb
<%# ERB Rails %>
<div class="flex flex-col gap-6 p-8 bg-[--color-surface]">
  <h1 class="text-2xl font-semibold text-[--color-text-primary]">
    <%= t('.title') %>
  </h1>
</div>
```

#### Regras de Conversão

| JSX/Paper | ERB Rails |
|-----------|-----------|
| `className=` | `class=` |
| Texto hardcoded | `t('.chave')` + entrada em `locales/` |
| `{variable}` | `<%= variable %>` |
| `list.map(item => ...)` | `<% @items.each do \|item\| %>` |
| `condition ? a : b` | `<% if condition %>...<% else %>...<% end %>` |
| Cores hardcoded (`#3b82f6`) | CSS Variables do design system (`bg-[--color-primary]`) |
| Valores Tailwind padrão | Verificar se há token equivalente no `@theme` |
| `onClick={handler}` | `data-controller` + `data-action` (Stimulus) |
| `href="/path"` | `<%= link_to ..., path %>` ou `<%= url_for ... %>` |

#### Dados Dinâmicos
```erb
<%# Loop Rails — substituir dados estáticos do design %>
<% @orders.each do |order| %>
  <%= render "orders/order_row", order: order %>
<% end %>

<%# Empty state — quando não há dados %>
<% if @orders.empty? %>
  <%= render "shared/empty_state",
        title: t('.empty.title'),
        description: t('.empty.description'),
        action_label: t('.empty.action'),
        action_path: new_order_path %>
<% end %>
```

#### IDs Dinâmicos
```erb
<%# Usar dom_id para IDs dinâmicos %>
<div id="<%= dom_id(order) %>">  <%# → "order_42" %>
<div id="<%= dom_id(@order, :edit_form) %>">  <%# → "edit_form_order_42" %>
```

### Passo 5: Adaptar Cores para Design System

O JSX do Paper pode ter cores hardcoded. Sempre mapeie para tokens:

```
bg-white         → bg-[--color-surface-elevated]
bg-gray-50       → bg-[--color-surface]
bg-gray-100      → bg-[--color-surface-sunken]
text-gray-900    → text-[--color-text-primary]
text-gray-500    → text-[--color-text-muted]
border-gray-200  → border-[--color-border]
bg-blue-600      → bg-[--color-primary]
hover:bg-blue-700 → hover:bg-[--color-primary-hover]
shadow-sm        → shadow-[--shadow-card]
shadow-lg        → shadow-[--shadow-elevated]
rounded-lg       → rounded-[--radius-lg]
rounded-md       → rounded-[--radius-md]
```

Se encontrar uma cor sem token equivalente, use o valor Tailwind padrão e documente
com um comentário `<%# TODO: adicionar token no design system %>`.

### Passo 6: Gerar Arquivos i18n

Para todo texto identificado na view:

```yaml
# config/locales/pt-BR/orders.yml
pt-BR:
  orders:
    index:
      title: "Pedidos"
      subtitle: "Gerencie todos os pedidos da plataforma"
      new_order: "Novo Pedido"
      empty:
        title: "Nenhum pedido encontrado"
        description: "Crie seu primeiro pedido para começar."
        action: "Criar Pedido"
    order_row:
      status:
        pending: "Pendente"
        confirmed: "Confirmado"
        cancelled: "Cancelado"
```

### Passo 7: Interatividade (Stimulus)

Para cada interação detectada no design (dropdown, modal, toggle, tab):

1. Verificar se há controller Stimulus existente em `app/javascript/controllers/`
2. Reutilizar se possível, criar novo se necessário
3. Seguir os padrões da skill `stimulus-patterns`

```erb
<%# Dropdown — Stimulus controller %>
<div data-controller="dropdown" class="relative">
  <button data-action="dropdown#toggle" class="...">
    Ações
  </button>
  <div data-dropdown-target="menu" class="hidden absolute ...">
    ...
  </div>
</div>
```

### Passo 8: Validação Final

Após gerar todos os arquivos:

1. **Comparação visual** — chame `get_screenshot` novamente e compare mentalmente com o ERB gerado
2. **Checklist de qualidade:**
   - [ ] Sem texto hardcoded — tudo em `t('.chave')`
   - [ ] Sem JavaScript inline — tudo em Stimulus controllers
   - [ ] Cores via CSS Variables do design system
   - [ ] `dom_id` para IDs dinâmicos
   - [ ] Empty states em todas as listagens
   - [ ] Partials extraídas para `shared/` quando reutilizáveis
   - [ ] Sem lógica de negócio nas views

---

## Integração com executa-task

Quando acionada por `executa-task`, esta skill:

1. É carregada no **Passo 2** da executa-task (Carregar Skills)
2. Executa durante o **Passo 5** (Implementação) para gerar views
3. Entrega: arquivos ERB + i18n + Stimulus controllers
4. A `executa-task` continua com: model, controller, routes, testes

```
executa-task
  Passo 2: carrega paper-to-rails (detectou paper-artboards.md)
  Passo 5: [paper-to-rails] converte artboards → ERB + i18n
           [rails-components] aplica partials e helpers
           [stimulus-patterns] adiciona interatividade
           implementa model, controller, routes, jobs
  Passo 6: testes (system tests validam visual com Capybara)
```

---

## Fallback: Sem Paper Disponível

Se o Paper MCP não estiver disponível durante `executa-task`, a skill informa:

> ⚠️ Paper MCP não detectado. As views serão implementadas com base na Tech Spec
> e nos padrões da skill `rails-visual-design`, sem referência ao Paper.
> Para usar o Paper, certifique-se que o Paper Desktop está aberto e o MCP configurado.

A `executa-task` prossegue normalmente sem o Paper.
