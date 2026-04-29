---
name: rails-animations
description: >-
  Implementa animações CSS e micro-interações para projetos Rails com Turbo.
  Garante que transições e animações funcionem corretamente com Turbo Drive
  (sem quebrar na navegação), Turbo Frames e Turbo Morph.
  Use quando precisar adicionar animações, transições de página, skeleton loaders,
  estados de loading, hover effects ou micro-interações em views Rails.
  Palavras-chave: "adicionar animação", "transição", "loading", "skeleton",
  "hover effect", "micro-interação", "fade", "slide", "animação suave".
  QUANDO NÃO: Para interatividade complexa com JS (ver stimulus-patterns) ou
  atualizações reativas do servidor (ver turbo-patterns).
license: MIT
compatibility: Rails 8.1+, Tailwind CSS v4, Turbo 8+, Stimulus 3.2+
---

Você é um especialista em animações CSS para Rails com Hotwire. Garante que toda animação
seja **Turbo-safe**, acessível (`prefers-reduced-motion`) e performática (só `transform` e `opacity`).

## Regras Fundamentais

1. **Apenas `transform` e `opacity`** — nunca anime `width`, `height`, `top`, `left` (causam reflow)
2. **Turbo-safe** — use `data-turbo-permanent` quando necessário; evite eventos `DOMContentLoaded`
3. **`prefers-reduced-motion` sempre** — animações são progressivas, não obrigatórias
4. **150–300ms** para micro-interações; máximo 400ms para transições complexas
5. **`ease-out` para entrada, `ease-in` para saída** — nunca `linear` em UI

---

## Problema Central: Turbo Drive + CSS Animations

Turbo Drive substitui o `<body>` sem recarregar a página — animações que dependem de
`DOMContentLoaded` ou classes adicionadas no load **não disparam** na navegação.

### ❌ Padrão Quebrado com Turbo

```javascript
// NUNCA faça isso — não funciona com Turbo Drive
document.addEventListener("DOMContentLoaded", () => {
  document.querySelectorAll(".animate-in").forEach(el => {
    el.classList.add("visible")
  })
})
```

### ✅ Padrão Correto: Turbo Events

```javascript
// Use os eventos do Turbo
document.addEventListener("turbo:load", () => {
  // Executado em toda navegação Turbo, não só no primeiro load
})

document.addEventListener("turbo:frame-load", (event) => {
  // Executado quando um Turbo Frame é carregado
  const frame = event.target
})
```

### ✅ Padrão Correto: Stimulus Controller

```javascript
// app/javascript/controllers/animate_controller.js
import { Controller } from "@hotwired/stimulus"

export default class extends Controller {
  connect() {
    // connect() é chamado SEMPRE que o elemento entra no DOM
    // Isso funciona perfeitamente com Turbo Drive, Frames e Streams
    this.element.classList.add("animate-in")
  }

  disconnect() {
    this.element.classList.remove("animate-in")
  }
}
```

```erb
<%# Uso — funciona com Turbo %>
<div data-controller="animate" class="opacity-0 translate-y-2 transition-all duration-300
     [&.animate-in]:opacity-100 [&.animate-in]:translate-y-0">
  Conteúdo animado
</div>
```

---

## Animações com CSS Puro (Tailwind)

### Hover States (sem JS — funciona com Turbo)

```erb
<%# Card com hover %>
<div class="group bg-(--color-surface-elevated) rounded-(--radius-lg)
            border border-(--color-border) shadow-(--shadow-card)
            hover:shadow-(--shadow-elevated) hover:border-(--color-border-strong)
            transition-all duration-200 cursor-pointer">
  <div class="p-6 space-y-3">
    <div class="group-hover:translate-x-0.5 transition-transform duration-200">
      <h3 class="font-semibold text-(--color-text-primary)">Título</h3>
    </div>
    <p class="text-sm text-(--color-text-muted)">Descrição</p>
  </div>
</div>

<%# Botão com feedback de press %>
<button class="active:scale-95 transition-transform duration-100">
  Clique aqui
</button>

<%# Link com underline animado %>
<a href="#" class="relative after:absolute after:bottom-0 after:left-0
                   after:h-px after:w-0 after:bg-(--color-primary)
                   after:transition-all after:duration-200
                   hover:after:w-full">
  Saiba mais
</a>
```

### Transições de Entrada (Stimulus + CSS)

```css
/* app/assets/stylesheets/application.css — adicionar após @theme */

/* Respeita preferência do usuário */
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
  }
}

/* Animações reutilizáveis */
@keyframes fade-in {
  from { opacity: 0; }
  to   { opacity: 1; }
}

@keyframes slide-up {
  from { opacity: 0; transform: translateY(0.5rem); }
  to   { opacity: 1; transform: translateY(0); }
}

@keyframes slide-down {
  from { opacity: 0; transform: translateY(-0.5rem); }
  to   { opacity: 1; transform: translateY(0); }
}

@keyframes scale-in {
  from { opacity: 0; transform: scale(0.95); }
  to   { opacity: 1; transform: scale(1); }
}

.animate-fade-in  { animation: fade-in  200ms ease-out both; }
.animate-slide-up { animation: slide-up 250ms ease-out both; }
.animate-scale-in { animation: scale-in 200ms ease-out both; }

/* Stagger delay para listas */
.animate-stagger > * { animation: slide-up 250ms ease-out both; }
.animate-stagger > *:nth-child(1)  { animation-delay: 0ms; }
.animate-stagger > *:nth-child(2)  { animation-delay: 50ms; }
.animate-stagger > *:nth-child(3)  { animation-delay: 100ms; }
.animate-stagger > *:nth-child(4)  { animation-delay: 150ms; }
.animate-stagger > *:nth-child(5)  { animation-delay: 200ms; }
.animate-stagger > *:nth-child(n+6) { animation-delay: 250ms; }
```

```erb
<%# Uso nas views — funciona com Turbo porque CSS animation reinicia no connect() %>
<div class="animate-fade-in">Aparece suavemente</div>

<ul class="animate-stagger space-y-2">
  <% @items.each do |item| %>
    <li class="...(classes do card)..."><%= item.name %></li>
  <% end %>
</ul>
```

---

## Transição de Página com Turbo Drive

```css
/* app/assets/stylesheets/application.css */

/* Turbo Drive — fade entre páginas */
@keyframes turbo-progress-fade-in {
  from { opacity: 0; transform: translateY(4px); }
  to   { opacity: 1; transform: translateY(0); }
}

html[data-turbo-preview] body {
  opacity: 0.6;
}

/* Anima o conteúdo após cada navegação */
.turbo-animate {
  animation: turbo-progress-fade-in 200ms ease-out both;
}
```

```erb
<%# No layout — envolver o yield %>
<main id="main-content" class="min-h-[calc(100vh-4rem)]">
  <div class="turbo-animate">
    <%= yield %>
  </div>
</main>
```

---

## Skeleton Loader (Turbo Frames lazy)

```erb
<%# Turbo Frame com skeleton enquanto carrega %>
<%= turbo_frame_tag "dashboard_stats", src: dashboard_stats_path, loading: :lazy do %>
  <%# Skeleton mostrado enquanto o frame carrega %>
  <div class="grid grid-cols-1 sm:grid-cols-3 gap-4 animate-pulse">
    <% 3.times do %>
      <div class="bg-(--color-surface-elevated) rounded-(--radius-lg)
                  border border-(--color-border) p-6 space-y-3">
        <div class="h-3 bg-(--color-surface-sunken) rounded-full w-1/2"></div>
        <div class="h-8 bg-(--color-surface-sunken) rounded-(--radius-sm) w-3/4"></div>
        <div class="h-3 bg-(--color-surface-sunken) rounded-full w-1/3"></div>
      </div>
    <% end %>
  </div>
<% end %>
```

---

## Estado de Loading em Botões (Stimulus)

```javascript
// app/javascript/controllers/loading_button_controller.js
import { Controller } from "@hotwired/stimulus"

export default class extends Controller {
  static targets = ["text", "spinner"]
  static classes = ["loading"]

  connect() {
    this.element.closest("form")?.addEventListener("submit", () => this.#setLoading())
  }

  #setLoading() {
    this.element.disabled = true
    this.element.classList.add(...this.loadingClasses)
    if (this.hasTextTarget)    this.textTarget.textContent = this.element.dataset.loadingText || "..."
    if (this.hasSpinnerTarget) this.spinnerTarget.classList.remove("hidden")

    // Reset quando Turbo completar
    document.addEventListener("turbo:submit-end", () => this.#resetLoading(), { once: true })
  }

  #resetLoading() {
    this.element.disabled = false
    this.element.classList.remove(...this.loadingClasses)
    if (this.hasSpinnerTarget) this.spinnerTarget.classList.add("hidden")
  }
}
```

```erb
<%# Botão com loading state %>
<%= form.submit data: {
      controller: "loading-button",
      loading_button_loading_class: "opacity-70 cursor-not-allowed",
      loading_text: t(".saving")
    },
    class: "inline-flex items-center gap-2 #{btn_class(variant: :primary)}" %>
```

---

## Modal com Animação (Turbo Frame + Stimulus)

```erb
<%# app/views/shared/_modal.html.erb %>
<%# locals: (title:, size: :md) %>
<div data-controller="modal"
     data-action="keydown.esc@window->modal#close turbo:frame-load->modal#open"
     class="fixed inset-0 z-(--z-modal) flex items-end sm:items-center justify-center p-4">

  <%# Backdrop %>
  <div data-modal-target="backdrop" data-action="click->modal#close"
       class="absolute inset-0 bg-black/40 opacity-0 transition-opacity duration-200"></div>

  <%# Painel %>
  <div data-modal-target="panel"
       class="relative bg-(--color-surface-elevated) rounded-(--radius-xl)
              shadow-(--shadow-modal) border border-(--color-border)
              w-full max-w-md opacity-0 translate-y-4 sm:translate-y-0 sm:scale-95
              transition-all duration-200">
    <div class="flex items-center justify-between px-6 py-4 border-b border-(--color-border)">
      <h2 class="text-base font-semibold text-(--color-text-primary)"><%= title %></h2>
      <button data-action="modal#close" aria-label="<%= t('shared.close') %>"
              class="p-1 rounded-(--radius-sm) text-(--color-text-muted)
                     hover:text-(--color-text-primary) hover:bg-(--color-surface-sunken)
                     transition-colors">✕</button>
    </div>
    <div class="p-6"><%= yield %></div>
  </div>
</div>
```

```javascript
// app/javascript/controllers/modal_controller.js
import { Controller } from "@hotwired/stimulus"

export default class extends Controller {
  static targets = ["backdrop", "panel"]

  open() {
    document.body.classList.add("overflow-hidden")
    this.backdropTarget.classList.replace("opacity-0", "opacity-100")
    this.panelTarget.classList.replace("opacity-0", "opacity-100")
    this.panelTarget.classList.replace("translate-y-4", "translate-y-0")
    this.panelTarget.classList.replace("scale-95", "scale-100")
  }

  close() {
    this.backdropTarget.classList.replace("opacity-100", "opacity-0")
    this.panelTarget.classList.replace("opacity-100", "opacity-0")
    document.body.classList.remove("overflow-hidden")
    setTimeout(() => {
      const frame = this.element.closest("turbo-frame")
      if (frame) frame.src = null
    }, 200)
  }
}
```

---

## `data-turbo-permanent` — Preservar Elementos

Use para elementos que NÃO devem reanimar ou ser substituídos pelo Turbo Drive:

```erb
<%# O elemento é preservado entre navegações — animações não disparam novamente %>
<div id="sidebar" data-turbo-permanent>
  <%= render "shared/sidebar" %>
</div>

<%# Flash — preservado para não sumir durante navegação %>
<div id="flash" data-turbo-permanent aria-live="polite">
  ...
</div>
```

---

## Checklist de Animações

- [ ] Nenhum `DOMContentLoaded` — usar `turbo:load` ou Stimulus `connect()`
- [ ] Apenas `transform` e `opacity` animados (zero reflow)
- [ ] `@media (prefers-reduced-motion: reduce)` desativa todas as animações
- [ ] Durações entre 150–300ms para micro-interações
- [ ] `data-turbo-permanent` em elementos que devem persistir entre páginas
- [ ] Skeleton loaders em Turbo Frames com `loading: :lazy`
- [ ] Estados de loading em botões de formulário
- [ ] `ease-out` para entrada, `ease-in` para saída
