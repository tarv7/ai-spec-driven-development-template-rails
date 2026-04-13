# Diretrizes de Desenvolvimento

> [Descrição curta do projeto]
> **Rails 8.1 · Ruby 4.0.2 · PostgreSQL · Tailwind CSS · Hotwire (Turbo + Stimulus)**

## Stack

- **Ruby** 4.0.2, **Rails** 8.1, **PostgreSQL**
- **Frontend:** Hotwire (Turbo + Stimulus), Tailwind CSS, Importmap (sem Node.js)
- **Testes:** Minitest + Fixtures (não RSpec, não FactoryBot)
- **Background Jobs:** Solid Queue (database-backed, sem Redis)
- **Cache:** Solid Cache | **WebSocket:** Solid Cable
- **Assets:** Propshaft + Importmap (sem Webpack/esbuild)
- **Deploy:** Kamal 2 + Thruster
- **CI:** GitHub Actions (`.github/workflows/ci.yml`)
- **Dev:** Dev Container (Docker Compose + PostgreSQL + Selenium)

## Arquitetura

```
app/
  controllers/     # Thin. Apenas 7 ações REST. Novo recurso para cada mudança de estado.
  models/          # Rich. Lógica de negócio, concerns, associações, validações.
  models/concerns/ # Comportamento horizontal: Closeable, Assignable, Searchable.
  views/           # ERB + Turbo Frames/Streams. Sem frameworks JS.
  jobs/            # Shallow. Chamam métodos do model, sem lógica própria.
  mailers/         # Minimal. Agrupam notificações, plain-text primeiro.
  javascript/controllers/  # Stimulus controllers para interatividade JS.
```

**Sem `app/services/`, `app/queries/`, `app/policies/`.** Lógica de negócio vive nos models. Formulários complexos usam nested attributes padrão do Rails.

## Regras Absolutas

| Regra | Detalhe |
|-------|---------|
| ✅ Checks antes de concluir | `bin/rubocop -a` → `bin/brakeman --no-pager` → `bin/bundler-audit` → `bin/rails test` |
| ✅ Sempre `bin/rails` | Nunca use `rails` diretamente — use binstubs em `bin/` |
| ✅ i18n obrigatório | Toda string visível ao usuário em `config/locales/*.yml` — use `t('.chave')` |
| ✅ Causa raiz | Nunca use workarounds — corrija o problema real |
| ❌ Sem git destrutivo | Nunca `git restore`, `git reset`, `git clean` sem permissão explícita |
| ❌ Sem JS inline | Toda interatividade via Stimulus controllers |
| ❌ Sem edição do `schema.rb` | Use migrações exclusivamente |
| ❌ Sem service objects | Lógica de negócio vive nos models com concerns |

## Comandos Essenciais

```bash
bin/dev                                      # Servidor + Tailwind watch
bin/rails test                               # Todos os testes
bin/rails test test/models/user_test.rb      # Arquivo específico
bin/rails test test/models/user_test.rb:14   # Linha específica
bin/rails test:system                        # Testes E2E (Capybara + Selenium)
bin/ci                                       # CI completo (rubocop + brakeman + tests)
bin/rubocop -a                               # Lint + autocorreção
bin/rails db:migrate                         # Executar migrações
bin/rails db:prepare                         # Criar + migrar (idempotente)
bin/rails console                            # Console interativo
bin/setup                                    # Setup completo
```

## Nomenclatura

| Tipo | Convenção | Exemplo |
|------|-----------|---------|
| Model | Singular PascalCase | `User`, `OrderItem` |
| Controller | Plural, aninhado por recurso | `Orders::ClosuresController` |
| Registro de estado | Substantivo descrevendo o estado | `Closure`, `Publication` |
| Concern | Adjetivo/-able | `Closeable`, `Assignable`, `Searchable` |
| Job | `Model::VerbJob` | `Order::NotifyJob` |
| Teste | `ModelTest` / `ControllerTest` | `UserTest`, `OrdersControllerTest` |
| Stimulus | `kebab-case` no HTML, `snake_case` no arquivo | `data-controller="image-gallery"` → `image_gallery_controller.js` |
| Tabelas | `snake_case` plural | `order_items`, `user_accounts` |

## Anti-padrões

| Não faça | Faça |
|----------|------|
| `app/services/` | Lógica nos models com concerns |
| Editar `schema.rb` | Criar migração |
| `and` / `or` | `&&` / `||` |
| JavaScript inline | Stimulus controller |
| Strings hardcoded em views | `t('.chave')` (i18n) |
| `rescue => e` genérico | Rescue de exceção específica |
| Sprockets / Webpack / esbuild | Propshaft + Importmap |
| `rails` direto | `bin/rails` |
| Pular lint/testes | `bin/rubocop -a` → `bin/rails test` |

## Guia de Estilo

- Condicionais expandidas ao invés de cláusulas de guarda (exceção: retornos antecipados de uma única linha no início do método)
- Ordenação de métodos: métodos de classe > instância pública (inicializar primeiro) > métodos privados
- Ordenar métodos privados pelo fluxo de invocação (ordem de chamada)
- Métodos bang (`!`) somente quando existir uma contraparte sem exceção
- Sem quebra de linha sob a palavra-chave `private`/`protected`; indentar o conteúdo sob ela
- Evitar objetos de serviço -- a lógica de domínio pertence a modelos com preocupações (serviços são aceitáveis ​​quando justificados)
- `belongs_to :creator, default: -> { Current.user }` para valores padrão de contexto
- `touch: true` em associações filhas para invalidação de cache

## Metodologia AI-SDD

O projeto segue **AI-Specification-Driven Development**:

- **Documentação:** `ai-sdd/` — product map, vision, roadmap
- **Tasks:** `tasks/prd-[feature-slug]/` — PRD + techspec por funcionalidade
- **Commands:** `.opencode/commands/` — automação (PRD, techspec, tasks, review)
- **Skills:** `.opencode/skills/` — padrões Rails (CRUD, auth, testing, Turbo, Stimulus, etc.)
- **Rules:** `.opencode/rules/` — convenções por camada (models, controllers, views, etc.)

Veja `.opencode/rules/` para convenções detalhadas por camada (models, controllers, views, testing, migrations, jobs, mailers, multi-tenancy, style).
