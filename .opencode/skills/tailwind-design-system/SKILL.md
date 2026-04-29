---
name: tailwind-design-system
description: >-
  Cria e mantém o design system com Tailwind CSS v4 para projetos Rails.
  Estabelece tokens de design (cores, tipografia, espaçamento, sombras) via
  CSS Variables no application.css. Use quando precisar definir ou atualizar
  a identidade visual do projeto, escolher uma paleta, configurar fontes ou
  criar um sistema de design consistente.
  Palavras-chave: "paleta de cores", "tipografia", "design tokens", "tema", 
  "identidade visual", "configurar Tailwind", "criar design system".
  QUANDO NÃO: Para componentes específicos (ver rails-components) ou animações (ver rails-animations).
license: MIT
compatibility: Tailwind CSS v4, Rails 8.1+, Propshaft
---

Você é um especialista em design systems com foco em Tailwind CSS v4 para Rails. Seu objetivo é
criar uma **base visual consistente e coesa** via CSS Variables — a fonte única da verdade para
cores, tipografia e espaçamento em todo o projeto.

## Fundamentos: Tailwind CSS v4 + Rails

No Tailwind v4, a configuração é **CSS-first** — tudo vai no `application.css` via `@theme`.
Não há mais `tailwind.config.js` obrigatório para tokens básicos.

```css
/* app/assets/stylesheets/application.css */
@import "tailwindcss";

@theme {
  /* Seus tokens aqui */
}
```

---

## Passo 1: Escolha do Tipo de Produto

Antes de definir tokens, identifique o tipo de produto para guiar as escolhas:

| Tipo | Estética | Paleta Base | Fontes Típicas |
|------|----------|-------------|----------------|
| **SaaS B2B** | Clean, profissional, neutro | Slate/Zinc + accent azul/violeta | Inter, Plus Jakarta Sans |
| **SaaS B2C** | Amigável, colorido, acessível | Cores vibrantes + neutros quentes | DM Sans, Outfit |
| **Dashboard/Admin** | Denso, funcional, sério | Cinzas neutros + accent sutil | Inter, Geist |
| **Landing/Marketing** | Impactante, ousado | Gradientes + cor primária forte | Sora, Cal Sans, Plus Jakarta |
| **Marketplace** | Confiável, limpo | Azul/verde + neutros | Inter, DM Sans |
| **Fintech** | Preciso, seguro, premium | Azul profundo + dourado/verde | Sora, Inter |
| **Saúde** | Acolhedor, limpo, confiável | Verde/azul suave + branco | DM Sans, Nunito |

---

## Passo 2: Paletas Prontas por Tipo

### SaaS B2B — "Slate & Indigo"
```css
@theme {
  --color-primary:       oklch(52% 0.22 264);   /* indigo-600 */
  --color-primary-hover: oklch(46% 0.22 264);   /* indigo-700 */
  --color-primary-light: oklch(95% 0.08 264);   /* indigo-50 */

  --color-success:       oklch(55% 0.18 142);
  --color-warning:       oklch(72% 0.19 65);
  --color-error:         oklch(52% 0.22 25);
  --color-info:          oklch(56% 0.19 230);

  --color-surface:          oklch(98% 0.005 264);
  --color-surface-elevated: oklch(100% 0 0);
  --color-surface-sunken:   oklch(95% 0.01 264);

  --color-text-primary:   oklch(13% 0.015 264);
  --color-text-secondary: oklch(35% 0.015 264);
  --color-text-muted:     oklch(55% 0.01 264);
  --color-text-disabled:  oklch(72% 0.01 264);

  --color-border:         oklch(88% 0.01 264);
  --color-border-strong:  oklch(75% 0.02 264);
}
```

### SaaS B2C — "Rose & Amber" (caloroso, jovem)
```css
@theme {
  --color-primary:       oklch(54% 0.23 15);    /* rose-600 */
  --color-primary-hover: oklch(48% 0.23 15);
  --color-primary-light: oklch(97% 0.08 15);

  --color-surface:          oklch(99% 0.005 50);
  --color-surface-elevated: oklch(100% 0 0);
  --color-surface-sunken:   oklch(97% 0.01 50);

  --color-text-primary:   oklch(14% 0.02 15);
  --color-text-secondary: oklch(38% 0.02 15);
  --color-text-muted:     oklch(58% 0.01 15);

  --color-border:         oklch(90% 0.01 50);
}
```

### Dashboard/Admin — "Zinc" (neutro denso)
```css
@theme {
  --color-primary:       oklch(56% 0.20 230);   /* sky-600 */
  --color-primary-hover: oklch(50% 0.20 230);
  --color-primary-light: oklch(96% 0.06 230);

  --color-surface:          oklch(97% 0 0);
  --color-surface-elevated: oklch(100% 0 0);
  --color-surface-sunken:   oklch(94% 0 0);
  --color-sidebar:          oklch(12% 0.01 264);
  --color-sidebar-text:     oklch(80% 0.01 264);
  --color-sidebar-active:   oklch(56% 0.20 230);

  --color-text-primary:   oklch(12% 0 0);
  --color-text-secondary: oklch(32% 0 0);
  --color-text-muted:     oklch(52% 0 0);

  --color-border:         oklch(90% 0 0);
}
```

### Fintech/Premium — "Slate & Emerald"
```css
@theme {
  --color-primary:       oklch(50% 0.18 156);   /* emerald-700 */
  --color-primary-hover: oklch(43% 0.18 156);
  --color-primary-light: oklch(96% 0.06 156);

  --color-accent:        oklch(72% 0.19 65);    /* âmbar/dourado */

  --color-surface:          oklch(98% 0.005 220);
  --color-surface-elevated: oklch(100% 0 0);

  --color-text-primary:   oklch(11% 0.02 220);
  --color-text-muted:     oklch(50% 0.01 220);

  --color-border:         oklch(88% 0.01 220);
}
```

---

## Passo 3: Sistema de Tipografia

### Escala de Tamanhos (padrão)

```css
@theme {
  /* Escala de tipo — base 16px */
  --text-xs:   0.75rem;    /* 12px */
  --text-sm:   0.875rem;   /* 14px */
  --text-base: 1rem;       /* 16px */
  --text-lg:   1.125rem;   /* 18px */
  --text-xl:   1.25rem;    /* 20px */
  --text-2xl:  1.5rem;     /* 24px */
  --text-3xl:  1.875rem;   /* 30px */
  --text-4xl:  2.25rem;    /* 36px */
  --text-5xl:  3rem;       /* 48px */
}
```

### Pares de Fontes Recomendados

| Par | Display | Body | Uso |
|-----|---------|------|-----|
| **Clássico SaaS** | Inter 600-700 | Inter 400 | Versátil, todo produto |
| **Premium B2B** | Plus Jakarta Sans 700 | Plus Jakarta Sans 400 | SaaS enterprise |
| **Tech/Dev** | Sora 600 | DM Sans 400 | Fintech, dev tools |
| **Amigável B2C** | Nunito 700 | DM Sans 400 | Saúde, educação, consumer |
| **Editorial** | Fraunces 700 | Instrument Sans 400 | Blog, marketing sofisticado |
| **Minimalista** | Geist 600 | Geist 400 | Dashboard, admin |

```css
/* Exemplo: Inter (Google Fonts) */
@theme {
  --font-display: "Inter", system-ui, sans-serif;
  --font-body:    "Inter", system-ui, sans-serif;
  --font-mono:    "JetBrains Mono", "Fira Code", monospace;
}
```

```erb
<%# No <head> do layout — preconnect para performance %>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
```

---

## Passo 4: Sombras, Raios e Bordas

```css
@theme {
  /* Border radius — consistência em todo projeto */
  --radius-xs: 0.25rem;   /* 4px  — badges, tags pequenas */
  --radius-sm: 0.375rem;  /* 6px  — inputs, botões pequenos */
  --radius-md: 0.625rem;  /* 10px — botões, cards pequenos */
  --radius-lg: 1rem;      /* 16px — cards principais */
  --radius-xl: 1.5rem;    /* 24px — modais, drawers */
  --radius-2xl: 2rem;     /* 32px — hero sections */

  /* Sombras semanticamente nomeadas */
  --shadow-xs:       0 1px 2px oklch(0% 0 0 / 5%);
  --shadow-sm:       0 1px 3px oklch(0% 0 0 / 8%), 0 1px 2px oklch(0% 0 0 / 6%);
  --shadow-card:     0 1px 3px oklch(0% 0 0 / 8%), 0 4px 16px oklch(0% 0 0 / 6%);
  --shadow-elevated: 0 4px 6px oklch(0% 0 0 / 7%), 0 12px 32px oklch(0% 0 0 / 10%);
  --shadow-modal:    0 8px 16px oklch(0% 0 0 / 10%), 0 32px 64px oklch(0% 0 0 / 15%);
}
```

---

## Passo 5: Tokens de Interação e Estado

```css
@theme {
  /* Transições padrão */
  --transition-fast:   150ms ease-out;
  --transition-base:   200ms ease-out;
  --transition-slow:   300ms ease-out;

  /* Z-index scale */
  --z-base:     0;
  --z-raised:   10;
  --z-dropdown: 100;
  --z-sticky:   200;
  --z-overlay:  300;
  --z-modal:    400;
  --z-toast:    500;
}
```

---

## Passo 6: Estados Semânticos (Alerts, Badge, Flash)

```css
@theme {
  /* Success */
  --color-success-bg:     oklch(97% 0.06 142);
  --color-success-text:   oklch(35% 0.18 142);
  --color-success-border: oklch(80% 0.14 142);

  /* Warning */
  --color-warning-bg:     oklch(98% 0.06 65);
  --color-warning-text:   oklch(40% 0.18 65);
  --color-warning-border: oklch(82% 0.14 65);

  /* Error */
  --color-error-bg:       oklch(97% 0.06 25);
  --color-error-text:     oklch(38% 0.20 25);
  --color-error-border:   oklch(80% 0.16 25);

  /* Info */
  --color-info-bg:        oklch(97% 0.06 230);
  --color-info-text:      oklch(38% 0.18 230);
  --color-info-border:    oklch(80% 0.14 230);
}
```

---

## Uso das Variáveis nas Views ERB

```erb
<%# Uso dos tokens como classes Tailwind v4 %>
<div class="bg-(--color-surface-elevated) text-(--color-text-primary)
            border border-(--color-border) rounded-(--radius-lg)
            shadow-(--shadow-card)">
  <h2 class="text-xl font-semibold text-(--color-text-primary)">
    Título
  </h2>
  <p class="text-sm text-(--color-text-muted)">
    Descrição
  </p>
</div>
```

---

## Checklist do Design System

- [ ] Todos os tokens definidos no `@theme` do `application.css`
- [ ] Paleta escolhida adequada ao tipo de produto
- [ ] Fontes importadas com `preconnect` para performance
- [ ] Sombras e raios consistentes (sem valores avulsos nas views)
- [ ] Estados semânticos (success, warning, error, info) definidos
- [ ] Nenhuma cor hex hardcoded diretamente nas views

---

## Anti-padrões

| Não faça | Faça |
|----------|------|
| `class="bg-indigo-600"` hardcoded | `class="bg-(--color-primary)"` |
| `@apply` extensivamente no CSS | Classes Tailwind diretamente no ERB |
| Tokens em `tailwind.config.js` | Tokens em `@theme` no CSS (v4) |
| Cores diferentes para o mesmo conceito | Um token, uma cor, consistência total |
| Sombras inventadas por view | `shadow-(--shadow-card)` do design system |
