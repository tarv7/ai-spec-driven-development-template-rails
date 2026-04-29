---
name: rails-components
description: >-
  Cria componentes de UI reutilizáveis e Rails-idiomáticos: partials ERB,
  helpers de classe, layouts aninhados e slots com content_for/yield.
  Use quando precisar criar, extrair ou organizar componentes de interface
  em um projeto Rails — botões, cards, navbars, sidebars, modais, formulários,
  empty states e afins.
  Palavras-chave: "crie um componente", "partial reutilizável", "botão",
  "card", "navbar", "sidebar", "modal", "formulário", "layout".
  QUANDO NÃO: Para animações (ver rails-animations), design tokens (ver tailwind-design-system)
  ou interatividade JS (ver stimulus-patterns).
license: MIT
compatibility: Rails 8.1+, Tailwind CSS v4, ERB, Hotwire
---

Você é um especialista em componentização Rails. Cria partials ERB reutilizáveis, helpers Ruby
para classes Tailwind e layouts bem estruturados — sem ViewComponent por padrão (use apenas quando
o isolamento/testabilidade justificar).

## Filosofia

**Partials primeiro.** Stack nativa do Rails:
- `render "shared/button"` para componentes simples
- `content_for` / `yield` para slots de layout  
- Helpers Ruby para lógica de classes CSS
- ViewComponent apenas para lógica complexa + alta reutilização testável

---

## Estrutura de Arquivos

```
app/
  views/shared/
    _navbar.html.erb
    _sidebar.html.erb
    _topbar.html.erb
    _footer.html.erb
    _flash.html.erb
    _page_header.html.erb
    _card.html.erb
    _empty_state.html.erb
    _modal.html.erb
  layouts/
    application.html.erb    ← público/marketing
    dashboard.html.erb      ← autenticado com sidebar
    auth.html.erb           ← login/signup centrado
  helpers/
    ui_helper.rb            ← helpers de classe Tailwind
    application_helper.rb   ← flash_class e utilitários gerais
```

---

## ui_helper.rb — Helpers de Classe

```ruby
# app/helpers/ui_helper.rb
module UiHelper
  def btn_class(variant: :primary, size: :md, extra: nil)
    base = "inline-flex items-center justify-center font-medium rounded-(--radius-md) " \
           "transition-colors duration-150 focus-visible:outline-none focus-visible:ring-2 " \
           "focus-visible:ring-(--color-primary) disabled:opacity-50 disabled:cursor-not-allowed"

    variants = {
      primary:   "bg-(--color-primary) text-white hover:bg-(--color-primary-hover) shadow-(--shadow-xs)",
      secondary: "bg-(--color-surface-sunken) text-(--color-text-primary) border border-(--color-border) hover:bg-(--color-border)",
      danger:    "bg-(--color-error-bg) text-(--color-error-text) border border-(--color-error-border) hover:opacity-90",
      ghost:     "text-(--color-text-secondary) hover:bg-(--color-surface-sunken) hover:text-(--color-text-primary)",
      link:      "text-(--color-primary) underline-offset-4 hover:underline p-0"
    }

    sizes = {
      xs: "text-xs gap-1 px-2.5 py-1.5",
      sm: "text-sm gap-1.5 px-3 py-2",
      md: "text-sm gap-2 px-4 py-2.5",
      lg: "text-base gap-2 px-5 py-3"
    }

    [base, variants.fetch(variant, variants[:primary]), sizes.fetch(size, sizes[:md]), extra].compact.join(" ")
  end

  def badge_class(variant = :neutral)
    base = "inline-flex items-center px-2.5 py-0.5 rounded-full text-xs font-medium"
    variants = {
      neutral: "bg-(--color-surface-sunken) text-(--color-text-secondary) ring-1 ring-(--color-border)",
      success: "bg-(--color-success-bg) text-(--color-success-text) ring-1 ring-(--color-success-border)",
      warning: "bg-(--color-warning-bg) text-(--color-warning-text) ring-1 ring-(--color-warning-border)",
      error:   "bg-(--color-error-bg) text-(--color-error-text) ring-1 ring-(--color-error-border)",
      primary: "bg-(--color-primary-light) text-(--color-primary) ring-1 ring-(--color-primary)/20"
    }
    "#{base} #{variants.fetch(variant, variants[:neutral])}"
  end
end
```

---

## Componentes Compartilhados

### Flash Messages

```erb
<%# app/views/shared/_flash.html.erb %>
<div id="flash" aria-live="polite" class="fixed top-4 right-4 z-(--z-toast) space-y-2">
  <% flash.each do |type, message| %>
    <% next if message.blank? %>
    <div data-controller="auto-dismiss" data-auto-dismiss-delay-value="4000"
         class="flex items-start gap-3 min-w-72 max-w-sm px-4 py-3
                rounded-(--radius-md) shadow-(--shadow-elevated) border text-sm font-medium
                <%= flash_class(type) %>" role="alert">
      <span class="flex-1"><%= message %></span>
      <button data-action="auto-dismiss#dismiss" aria-label="<%= t('shared.dismiss') %>"
              class="shrink-0 opacity-60 hover:opacity-100 transition-opacity">✕</button>
    </div>
  <% end %>
</div>
```

```ruby
# app/helpers/application_helper.rb (método adicional)
def flash_class(type)
  {
    "notice" => "bg-(--color-success-bg) text-(--color-success-text) border-(--color-success-border)",
    "alert"  => "bg-(--color-error-bg) text-(--color-error-text) border-(--color-error-border)",
    "info"   => "bg-(--color-info-bg) text-(--color-info-text) border-(--color-info-border)",
    "warn"   => "bg-(--color-warning-bg) text-(--color-warning-text) border-(--color-warning-border)"
  }.fetch(type.to_s, "bg-(--color-surface-elevated) text-(--color-text-primary) border-(--color-border)")
end
```

### Page Header

```erb
<%# app/views/shared/_page_header.html.erb %>
<%# locals: (title:, subtitle: nil, actions: nil) %>
<div class="flex flex-col sm:flex-row sm:items-start sm:justify-between gap-4 mb-8">
  <div class="space-y-1">
    <h1 class="text-2xl font-semibold tracking-tight text-(--color-text-primary)">
      <%= title %>
    </h1>
    <% if subtitle.present? %>
      <p class="text-sm text-(--color-text-muted)"><%= subtitle %></p>
    <% end %>
  </div>
  <% if actions.present? %>
    <div class="flex items-center gap-2 shrink-0"><%= actions %></div>
  <% end %>
</div>
```

### Card

```erb
<%# app/views/shared/_card.html.erb %>
<%# locals: (title: nil, subtitle: nil, padding: true) %>
<div class="bg-(--color-surface-elevated) rounded-(--radius-lg)
            border border-(--color-border) shadow-(--shadow-card) overflow-hidden">
  <% if title.present? %>
    <div class="px-6 py-4 border-b border-(--color-border)">
      <h3 class="text-base font-semibold text-(--color-text-primary)"><%= title %></h3>
      <% if subtitle.present? %>
        <p class="text-sm text-(--color-text-muted) mt-0.5"><%= subtitle %></p>
      <% end %>
    </div>
  <% end %>
  <div class="<%= padding ? 'p-6' : '' %>"><%= yield %></div>
</div>
```

### Empty State

```erb
<%# app/views/shared/_empty_state.html.erb %>
<%# locals: (title:, description: nil, action_label: nil, action_path: nil) %>
<div class="flex flex-col items-center justify-center py-16 px-6 text-center">
  <h3 class="text-base font-semibold text-(--color-text-primary) mb-1.5"><%= title %></h3>
  <% if description.present? %>
    <p class="text-sm text-(--color-text-muted) max-w-sm leading-relaxed mb-5"><%= description %></p>
  <% end %>
  <% if action_label.present? && action_path.present? %>
    <%= link_to action_label, action_path,
          class: "inline-flex items-center gap-2 px-4 py-2.5 rounded-(--radius-md)
                  bg-(--color-primary) text-white text-sm font-medium
                  hover:bg-(--color-primary-hover) transition-colors" %>
  <% end %>
</div>
```

---

## Layouts

### Dashboard (sidebar + topbar)

```erb
<%# app/views/layouts/dashboard.html.erb %>
<!DOCTYPE html>
<html lang="<%= I18n.locale %>" class="h-full">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title><%= content_for?(:title) ? "#{yield(:title)} — #{t('app.name')}" : t('app.name') %></title>
    <%= csrf_meta_tags %><%= csp_meta_tag %>
    <%= stylesheet_link_tag "application", "data-turbo-track": "reload" %>
    <%= javascript_importmap_tags %>
  </head>
  <body class="h-full bg-(--color-surface) font-body antialiased">
    <div class="flex h-full">
      <%= render "shared/sidebar" %>
      <div class="flex-1 flex flex-col min-w-0">
        <%= render "shared/topbar" %>
        <main id="main-content" class="flex-1 overflow-auto">
          <%= render "shared/flash" %>
          <div class="mx-auto px-4 sm:px-6 lg:px-8 py-8 max-w-7xl"><%= yield %></div>
        </main>
      </div>
    </div>
  </body>
</html>
```

### Auth (centrado)

```erb
<%# app/views/layouts/auth.html.erb %>
<!DOCTYPE html>
<html lang="<%= I18n.locale %>" class="h-full">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title><%= content_for?(:title) ? "#{yield(:title)} — #{t('app.name')}" : t('app.name') %></title>
    <%= csrf_meta_tags %><%= csp_meta_tag %>
    <%= stylesheet_link_tag "application", "data-turbo-track": "reload" %>
    <%= javascript_importmap_tags %>
  </head>
  <body class="min-h-full bg-(--color-surface) font-body antialiased
               flex items-center justify-center px-4 py-16">
    <div class="w-full max-w-sm">
      <div class="text-center mb-8">
        <%= link_to root_path,
              class: "text-2xl font-bold text-(--color-text-primary)" do %>
          <%= t("app.name") %>
        <% end %>
      </div>
      <%= render "shared/flash" %>
      <%= yield %>
    </div>
  </body>
</html>
```

---

## Padrão de Campo de Formulário

```erb
<div class="space-y-1.5">
  <%= form.label :email, t(".email"),
        class: "block text-sm font-medium text-(--color-text-primary)" %>
  <%= form.email_field :email,
        class: "block w-full rounded-(--radius-md) border border-(--color-border)
                bg-(--color-surface-elevated) px-3 py-2.5 text-sm
                text-(--color-text-primary) placeholder-(--color-text-disabled)
                focus:outline-none focus:ring-2 focus:ring-(--color-primary)
                focus:border-transparent transition-shadow",
        aria: { describedby: "email_error" } %>
  <% if form.object.errors[:email].any? %>
    <p id="email_error" class="text-sm text-(--color-error-text)" role="alert">
      <%= form.object.errors[:email].first %>
    </p>
  <% end %>
</div>
```

---

## Quando Usar ViewComponent

Use `gem "view_component"` somente quando:
- O componente tem lógica Ruby complexa
- Precisa de testes unitários isolados
- É reutilizado em 5+ lugares com variações significativas

```bash
bin/rails generate component Button variant:string size:string
```

---

## Checklist

- [ ] Partials em `shared/` para componentes multi-view
- [ ] `content_for(:title)` em todas as views
- [ ] Flash messages com `aria-live="polite"`
- [ ] Empty states em todas as listagens
- [ ] Erros de formulário inline (abaixo do campo)
- [ ] Layouts separados por contexto (app / dashboard / auth)
