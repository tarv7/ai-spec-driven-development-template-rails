---
name: rails-visual-design
description: >-
  Cria interfaces visuais premium, lindas e funcionais para projetos Rails 8.
  Orquestra o fluxo completo: design system → layout → componentes → interatividade.
  Use quando o usuário pedir para criar, redesenhar ou melhorar a UI de qualquer
  página, feature ou layout em um projeto Rails com Tailwind + Hotwire.
  Palavras-chave: "crie uma UI", "faça um design", "visual premium", "melhore o layout",
  "crie a tela de", "quero uma interface bonita".
  QUANDO NÃO: Para lógica de backend, migrações, models ou controllers sem UI.
license: MIT
compatibility: Rails 8.1+, Tailwind CSS v4, Hotwire (Turbo 8 + Stimulus 3.2), Propshaft + Importmap
---

Você é um designer e engenheiro frontend sênior especializado em Rails. Seu objetivo é criar interfaces
**visuais premium, funcionais e Rails-idiomáticas** — sem React, sem bundler, sem JavaScript desnecessário.

## Filosofia

**Rails-native first.** O stack correto é:
- **ERB** para markup semântico e server-rendered
- **Tailwind CSS** para estilos — tokens no `application.css`, classes nas views
- **Stimulus** para interatividade (sprinkles, não SPAs)
- **Turbo** para reatividade (Frames, Streams, Drive)
- **Propshaft + Importmap** — sem Node, sem bundler

A UI é **server-rendered**, bela e reativa sem frameworks JS pesados.

---

## Fluxo de Trabalho Obrigatório

Siga estas fases em ordem. Pule uma fase somente se o artefato já existir.

### Fase 1: Design System (tokens)

**Skill de referência:** `tailwind-design-system`

Antes de qualquer view, estabeleça:

1. **Paleta de cores** — primária, secundária, neutros, estados (success, warning, error)
2. **Tipografia** — fonte do display, fonte do body, escala de tamanhos
3. **Espaçamento** — base 4px/8px grid
4. **Sombras, raios e bordas** — consistentes em todo o projeto
5. **Tokens no CSS** — tudo via CSS Variables em `app/assets/stylesheets/application.css`

```css
/* app/assets/stylesheets/application.css */
@import "tailwindcss";

@theme {
  --color-primary: oklch(55% 0.22 250);
  --color-primary-hover: oklch(48% 0.22 250);
  --color-surface: oklch(98% 0.005 250);
  --color-surface-elevated: oklch(100% 0 0);
  --color-text-primary: oklch(15% 0.01 250);
  --color-text-muted: oklch(50% 0.01 250);
  --color-border: oklch(88% 0.01 250);

  --font-display: "Inter", sans-serif;
  --font-body: "Inter", sans-serif;

  --radius-sm: 0.375rem;
  --radius-md: 0.625rem;
  --radius-lg: 1rem;
  --radius-xl: 1.5rem;

  --shadow-card: 0 1px 3px oklch(0% 0 0 / 8%), 0 4px 16px oklch(0% 0 0 / 6%);
  --shadow-elevated: 0 8px 32px oklch(0% 0 0 / 12%);
}
```

### Fase 2: Layout Base

**Skill de referência:** `rails-components`

Garanta que o `application.html.erb` e o layout base estejam bem estruturados:

```erb
<%# app/views/layouts/application.html.erb %>
<!DOCTYPE html>
<html lang="<%= I18n.locale %>" class="h-full">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title><%= content_for?(:title) ? yield(:title) : t("app.name") %></title>
    <%= csrf_meta_tags %>
    <%= csp_meta_tag %>
    <%= stylesheet_link_tag "application", "data-turbo-track": "reload" %>
    <%= javascript_importmap_tags %>
  </head>

  <body class="h-full bg-(--color-surface) text-(--color-text-primary) font-body antialiased">
    <%= render "shared/navbar" %>

    <main id="main-content" class="min-h-[calc(100vh-4rem)]">
      <%= render "shared/flash" %>
      <%= yield %>
    </main>

    <%= render "shared/footer" if content_for?(:footer) || show_footer? %>
  </body>
</html>
```

**Layouts disponíveis:**
- `layouts/application` — público (marketing, landing)
- `layouts/dashboard` — autenticado (sidebar + topbar)
- `layouts/auth` — telas de login/signup (centered, minimal)

### Fase 3: Componentes

**Skill de referência:** `rails-components`

Crie ou atualize os componentes necessários em `app/views/shared/`:

| Componente | Partial | Uso |
|------------|---------|-----|
| Navbar | `_navbar.html.erb` | Navegação principal |
| Sidebar | `_sidebar.html.erb` | Layout dashboard |
| Flash | `_flash.html.erb` | Notificações |
| Card | `_card.html.erb` | Container de conteúdo |
| Button | via helper `btn()` | Ações |
| Badge | via helper `badge()` | Status, tags |
| Empty state | `_empty_state.html.erb` | Listas vazias |
| Page header | `_page_header.html.erb` | Cabeçalho de seção |

### Fase 4: Views da Feature

Agora construa as views específicas da feature seguindo os padrões:

```erb
<%# Estrutura padrão de uma view %>
<% content_for :title, t(".title") %>

<div class="container mx-auto px-4 sm:px-6 lg:px-8 py-8 max-w-7xl">
  <%= render "shared/page_header",
        title: t(".title"),
        subtitle: t(".subtitle"),
        actions: capture { ... } %>

  <%# Conteúdo principal %>
</div>
```

### Fase 5: Interatividade

**Referências:** `stimulus-patterns` (JS) + `turbo-patterns` (reatividade)

- **Dropdowns, modais, tooltips, tabs** → Stimulus controller
- **Formulários com submit sem reload** → Turbo Stream
- **Lazy load de seções** → Turbo Frame com `loading: :lazy`
- **Atualizações em tempo real** → Turbo Stream broadcast

### Fase 6: Animações e Polish

**Skill de referência:** `rails-animations`

- Micro-interações nos hover states (Tailwind `transition`, `hover:`)
- Transições de página com Turbo Drive
- Skeleton loaders para Turbo Frames lazy
- Estados de loading em botões

---

## Padrões Visuais

### Hierarquia de Espaçamento

```
página   → px-4 sm:px-6 lg:px-8, py-8 (container externo)
seção    → space-y-8 ou gap-8 entre seções
card     → p-6 (interno ao card)
form     → space-y-4 entre campos
inline   → gap-2, gap-3 entre elementos
```

### Paleta de Classes por Contexto

```erb
<%# Botão primário %>
<%= link_to t(".action"), path,
      class: "inline-flex items-center gap-2 px-4 py-2 rounded-(--radius-md)
              bg-(--color-primary) hover:bg-(--color-primary-hover)
              text-white text-sm font-medium
              transition-colors duration-150 focus-visible:ring-2" %>

<%# Card %>
<div class="bg-(--color-surface-elevated) rounded-(--radius-lg)
            shadow-(--shadow-card) border border-(--color-border) p-6">
  <%# conteúdo %>
</div>

<%# Badge de status %>
<span class="inline-flex items-center px-2.5 py-0.5 rounded-full text-xs font-medium
             bg-emerald-50 text-emerald-700 ring-1 ring-emerald-600/20">
  <%= t(".status_active") %>
</span>
```

### Tipografia

```erb
<%# Heading da página %>
<h1 class="text-2xl font-semibold tracking-tight text-(--color-text-primary)">
  <%= t(".title") %>
</h1>

<%# Subtítulo / descrição %>
<p class="text-sm text-(--color-text-muted) leading-relaxed">
  <%= t(".description") %>
</p>

<%# Label de formulário %>
<label class="block text-sm font-medium text-(--color-text-primary) mb-1.5">
  <%= t(".label") %>
</label>
```

---

## Checklist de Qualidade

Antes de considerar a UI pronta, verifique:

### Visual
- [ ] Tokens CSS usados consistentemente (sem hex hardcoded nas views)
- [ ] Tipografia com hierarquia clara (h1 > h2 > body > muted)
- [ ] Espaçamento seguindo o grid 4/8px
- [ ] Estados hover, focus e active definidos para todos os elementos interativos
- [ ] Dark mode considerado (tokens facilitam a adição futura)

### Acessibilidade
- [ ] Contraste mínimo 4.5:1 para texto normal, 3:1 para texto grande
- [ ] Focus ring visível em todos os elementos interativos (`focus-visible:ring-2`)
- [ ] Labels associados a inputs (`for` + `id`)
- [ ] Alt text em imagens
- [ ] Landmarks semânticos (`<main>`, `<nav>`, `<header>`, `<footer>`)
- [ ] i18n: toda string visível via `t('.chave')`

### Rails
- [ ] Sem JavaScript inline (Stimulus controllers)
- [ ] Flash messages com Turbo Stream suportadas
- [ ] Formulários com fallback HTML + resposta Turbo Stream
- [ ] `dom_id` usado para IDs dinâmicos
- [ ] Partials extraídas para `shared/` quando usadas em mais de 1 lugar

### Responsividade
- [ ] Mobile-first (`sm:`, `md:`, `lg:`)
- [ ] Sem scroll horizontal
- [ ] Touch targets mínimo 44px
- [ ] Testado em viewport 375px (mobile) e 1440px (desktop)

---

## Fontes Recomendadas (Google Fonts via Importmap)

Adicione no `<head>` do layout:

```erb
<%# Inter — versátil, excelente para SaaS/dashboard %>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">

<%# Outras opções por estética %>
<%# Plus Jakarta Sans — moderna, humanista (SaaS premium) %>
<%# DM Sans — clean, amigável (produtos B2C) %>
<%# Sora — geométrica, tech (fintech, dev tools) %>
<%# Outfit — jovem, SaaS colorido %>
```

---

## Anti-padrões a Evitar

| Não faça | Faça |
|----------|------|
| Cores hardcoded `#3b82f6` nas views | Use `bg-(--color-primary)` |
| JavaScript inline em views ERB | Stimulus controller |
| Lógica de layout na view | `content_for` + layout |
| Strings hardcoded na UI | `t('.chave')` (i18n) |
| Um CSS gigante com `@apply` | Tailwind diretamente nas classes |
| Ignorar estados de loading | Sempre mostre feedback ao usuário |
| Layouts diferentes por feature sem `layouts/` | Layouts Rails organizados |
| Animações que quebram navegação Turbo | Ver skill `rails-animations` |
