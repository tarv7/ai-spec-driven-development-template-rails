# AGENTS.md — Diretrizes de Desenvolvimento

> [Descrição curta do projeto]
> **Rails 8.1 · Ruby 4.0.2 · PostgreSQL · Tailwind CSS · Hotwire (Turbo + Stimulus)**

---

## 1. Regras Absolutas

| Regra | Detalhe |
|-------|---------|
| ✅ **Checks antes de concluir** | `bin/rubocop -a` (lint + autofix) → `bin/brakeman --no-pager` (segurança) → `bin/bundler-audit` (gems) → `bin/rails test` (testes) |
| ✅ **Sempre `bin/rails`** | Nunca use `rails` diretamente — use os binstubs em `bin/` |
| ✅ **i18n obrigatório** | Toda string visível ao usuário deve estar em `config/locales/*.yml` |
| ✅ **Causa raiz** | Nunca use workarounds — corrija o problema real |
| ❌ **Sem git destrutivo** | Nunca execute `git restore`, `git reset`, `git clean` ou similares sem permissão explícita do usuário |
| ❌ **Sem JS inline** | Toda interatividade via Stimulus controllers em `app/javascript/controllers/` |
| ❌ **Sem edição manual do `schema.rb`** | Use migrações exclusivamente |

---

## 2. Stack do Projeto

| Área | Tecnologia | Notas |
|------|-----------|-------|
| Framework | Rails 8.1 | `config.load_defaults 8.1` |
| Linguagem | Ruby 4.0.2 | Definido em `.ruby-version` |
| Banco de Dados | PostgreSQL | Dev: `[app_name]_development` / Test: `[app_name]_test` |
| Asset Pipeline | Propshaft | Substituiu Sprockets no Rails 8 |
| JavaScript | Importmap | Sem bundler JS (Webpack/esbuild) — pins em `config/importmap.rb` |
| Frontend | Tailwind CSS | Via `tailwindcss-rails` gem; CSS em `app/assets/tailwind/application.css` |
| Interatividade | Hotwire (Turbo + Stimulus) | Stimulus controllers em `app/javascript/controllers/` |
| JSON API | Jbuilder | Templates `.json.jbuilder` |
| Background Jobs | Solid Queue | Roda dentro do Puma em dev; banco dedicado em produção |
| Cache | Solid Cache | Banco dedicado em produção |
| WebSocket | Solid Cable | Banco dedicado em produção |
| Processamento de Imagens | ImageProcessing + libvips | Gem `image_processing`, requer `libvips` no sistema |
| Deploy | Kamal (Docker) | Configuração em `config/deploy.yml` |
| HTTP Acceleration | Thruster | Cache de assets + compressão + X-Sendfile |
| CI | GitHub Actions | `.github/workflows/ci.yml` — scan, lint, test, system-test |
| Testes | Minitest + Capybara + Selenium | Unit + functional + system (E2E) |
| Linting | RuboCop Omakase | `rubocop-rails-omakase` — estilo padrão Rails |
| Dev Environment | Dev Container | Docker Compose com PostgreSQL + Selenium |

---

## 3. Comandos Essenciais

### Desenvolvimento

```bash
bin/dev                          # Inicia servidor + Tailwind watch (via Foreman/Procfile.dev)
bin/rails server                 # Apenas servidor (porta 3000)
bin/rails console                # Console interativo Rails
bin/setup                        # Setup completo (deps + db + inicia servidor)
bin/setup --skip-server          # Setup sem iniciar servidor
bin/setup --reset                # Setup com db:reset
```

### Banco de Dados

```bash
bin/rails db:create              # Criar banco de dados
bin/rails db:migrate             # Executar migrações pendentes
bin/rails db:rollback            # Desfazer última migração
bin/rails db:seed                # Popular banco com dados iniciais
bin/rails db:prepare             # Criar + migrar (idempotente)
bin/rails db:reset               # Drop + create + migrate + seed
bin/rails db:test:prepare        # Preparar banco de teste
```

### Testes

```bash
bin/rails test                                    # Todos os testes (unit + functional)
bin/rails test test/models/                       # Testes de modelos
bin/rails test test/controllers/                  # Testes de controllers
bin/rails test:system                             # Testes de sistema (E2E com Capybara)
bin/rails test test/models/user_test.rb           # Arquivo específico
bin/rails test test/models/user_test.rb:10        # Teste na linha 10
bin/rails test -v                                 # Saída detalhada
```

### Qualidade de Código e Segurança

```bash
bin/rubocop                      # Executar RuboCop (lint)
bin/rubocop -a                   # Lint + autocorreção
bin/brakeman --no-pager          # Análise estática de segurança (Brakeman)
bin/bundler-audit                # Auditoria de vulnerabilidades em gems
bin/importmap audit              # Auditoria de dependências JavaScript
bin/ci                           # Pipeline CI completo local (setup → lint → security → tests)
```

### Deploy

```bash
bin/kamal deploy                 # Deploy via Kamal
bin/kamal console                # Console remoto
bin/kamal logs                   # Logs em tempo real
bin/kamal shell                  # Shell interativo no container
```

---

## 4. Estrutura do Projeto

```
/
├── app/
│   ├── assets/
│   │   ├── builds/              # Output compilado (gerado automaticamente)
│   │   ├── images/              # Imagens estáticas
│   │   ├── stylesheets/         # CSS customizado (application.css)
│   │   └── tailwind/            # Arquivo raiz Tailwind (application.css)
│   ├── controllers/             # Controllers Rails
│   │   └── concerns/            # Concerns de controller
│   ├── helpers/                 # View helpers
│   ├── javascript/
│   │   ├── application.js       # Entry point JS (importmap)
│   │   └── controllers/         # Stimulus controllers
│   │       ├── application.js   # Base Stimulus
│   │       ├── index.js         # Auto-registro de controllers
│   │       └── hello_controller.js
│   ├── jobs/                    # Background jobs (Solid Queue)
│   ├── mailers/                 # Action Mailer classes
│   ├── models/                  # ActiveRecord models
│   │   └── concerns/            # Model concerns
│   └── views/
│       ├── layouts/             # application.html.erb, mailer layouts
│       └── pwa/                 # PWA manifest e service worker
├── config/
│   ├── application.rb           # Configuração principal
│   ├── routes.rb                # Definições de rotas
│   ├── database.yml             # Configuração PostgreSQL
│   ├── deploy.yml               # Configuração Kamal
│   ├── importmap.rb             # Pins de pacotes JS
│   ├── ci.rb                    # Pipeline CI local (bin/ci)
│   ├── environments/            # Configurações por ambiente
│   ├── initializers/            # Inicializadores (assets, CSP, etc.)
│   └── locales/                 # Arquivos i18n (en.yml, pt-BR.yml)
├── db/
│   ├── seeds.rb                 # Dados de seed
│   ├── cable_schema.rb          # Schema Solid Cable
│   ├── cache_schema.rb          # Schema Solid Cache
│   └── queue_schema.rb          # Schema Solid Queue
├── test/
│   ├── test_helper.rb           # Configuração base dos testes
│   ├── application_system_test_case.rb  # Config testes de sistema (Capybara + Selenium)
│   ├── controllers/             # Testes de controllers
│   ├── fixtures/                # Dados de teste (YAML)
│   ├── helpers/                 # Testes de helpers
│   ├── integration/             # Testes de integração
│   ├── mailers/                 # Testes de mailers
│   ├── models/                  # Testes de modelos
│   └── system/                  # Testes de sistema (E2E)
├── tasks/                       # Tarefas técnicas (AI-SDD)
│   └── prd-[feature-slug]/      # PRD + techspec por funcionalidade
├── ai-sdd/                      # Documentação AI-SDD (product map, vision, roadmap)
├── .devcontainer/               # Dev Container (Docker Compose + PostgreSQL + Selenium)
├── .github/workflows/ci.yml     # GitHub Actions CI pipeline
├── .rubocop.yml                 # RuboCop config (herda rubocop-rails-omakase)
├── .example.env                 # Template de variáveis de ambiente
├── Dockerfile                   # Build de produção
├── Procfile.dev                 # Processos dev (web + css watcher)
└── opencode.json                # MCP servers (GitHub, Context7)
```

---

## 5. Convenções de Código

### Nomenclatura

| Tipo | Convenção | Exemplo |
|------|-----------|---------|
| Classes/Módulos | `PascalCase` | `UserAccount`, `OrderItem` |
| Métodos/variáveis | `snake_case` | `total_price`, `find_by_email` |
| Tabelas do banco | `snake_case` plural | `properties`, `user_accounts` |
| Arquivos Ruby | `snake_case` | `user_account.rb` |
| Stimulus controllers | `kebab-case` no HTML, `snake_case` no arquivo | `data-controller="image-gallery"` → `image_gallery_controller.js` |
| Rotas | `snake_case` plural para resources | `resources :properties` |

### Padrões Rails

- Herde de `ApplicationRecord`, `ApplicationController`, `ApplicationJob`
- Use **strong parameters** em controllers — nunca permita mass assignment direto
- Use **objetos de serviço** (`app/services/`) para lógica de negócio complexa
- Use **concerns** para funcionalidade compartilhada entre models/controllers
- Use **callbacks** com moderação — prefira serviços para efeitos colaterais complexos
- Use **scopes** para queries reutilizáveis nos models

### Internacionalização (i18n)

```ruby
# ✅ Correto — lazy lookup sempre que possível (resolve via caminho)
<%= t('.title') %>

# ✅ Correto — chave explícita no controller (caso não seja possível usar lazy lookup)
flash[:notice] = I18n.t('resources.created_successfully')

# ❌ Errado — string hardcoded
flash[:notice] = "Recurso criado com sucesso"
```

- Organize chaves em `config/locales/` seguindo `locale.escopo.chave`
- Crie arquivos separados por domínio se necessário (`resources.pt-BR.yml`)
- Locale padrão: verificar `config/application.rb` (por padrão `:en`)

### Formatação (RuboCop Omakase)

- **2 espaços** de indentação (sem tabs)
- Máximo **120 caracteres** por linha
- Use `&&` / `||` — nunca `and` / `or`
- Prefira arrays `%w[a b]` ao invés de `["a", "b"]` sempre que possível
- Evite números mágicos — extraia para constantes
- Sem espaços dentro de colchetes: `[a, [b, c]]`

### Tratamento de Erros

```ruby
# ✅ Rescue específico
rescue ActiveRecord::RecordNotFound => e
  logger.error("Registro não encontrado: #{e.message}")
  redirect_to root_path

# ❌ Rescue genérico
rescue => e
  # nunca faça isso
end
```

- Use `present?` / `blank?` em vez de `.nil?` ou `.empty?`
- Use `&.` (safe navigation) para method chaining em objetos potencialmente nil
- Log erros com contexto nos controllers e jobs
- Levante exceções com `raise` para erros que devem ser capturados por handlers

---

## 6. Testes

### Estrutura

| Tipo | Localização | Framework | Quando usar |
|------|-------------|-----------|-------------|
| Unit | `test/models/` | Minitest | Validações, scopes, métodos de model |
| Controller | `test/controllers/` | Minitest | Ações, redirecionamentos, status codes |
| Integration | `test/integration/` | Minitest | Fluxos multi-controller |
| System (E2E) | `test/system/` | Capybara + Selenium | Interações completas do usuário |
| Mailer | `test/mailers/` | Minitest | Envio e conteúdo de emails |
| Helper | `test/helpers/` | Minitest | View helpers |

### Convenções

- Nomeie `test/models/user_test.rb` para o model `User`
- Use **fixtures** em `test/fixtures/` para dados de teste
- Testes de sistema usam `ApplicationSystemTestCase` (headless Chrome via Selenium)
- Execute `bin/rails test` para unit + functional, `bin/rails test:system` para E2E

---

## 7. Ambiente de Desenvolvimento (Dev Container)

O projeto usa **Dev Containers** com Docker Compose:

| Serviço | Detalhes |
|---------|---------|
| `rails-app` | Container principal com Ruby 4.0.2 |
| `postgres` | PostgreSQL (host: `postgres` via `DB_HOST`) |
| `selenium` | Chrome headless para testes de sistema |

### Setup inicial

```bash
# Dentro do dev container, tudo é configurado automaticamente via:
bin/setup --skip-server
# Depois:
bin/dev
```

### Portas

- `3000` — Servidor Rails
- `5432` — PostgreSQL
- `45678` — Capybara server (testes de sistema)

---

## 8. CI/CD

### GitHub Actions (`.github/workflows/ci.yml`)

Pipeline com 4 jobs paralelos:

1. **scan_ruby** — Brakeman + bundler-audit
2. **scan_js** — `bin/importmap audit`
3. **lint** — RuboCop com cache
4. **test** — `bin/rails test` com PostgreSQL service
5. **system-test** — `bin/rails test:system` com PostgreSQL + screenshots em falha

### CI Local

```bash
bin/ci  # Executa: setup → rubocop → bundler-audit → importmap audit → brakeman → tests → seeds
```

---

## 9. Regras de Componentes Frontend

1. **Views ERB** — use templates `.html.erb`, nunca `.html` puro
2. **Partial views** — extraia componentes reutilizáveis como partials `_nome.html.erb`
3. **Stimulus controllers** — toda interatividade JS via controllers em `app/javascript/controllers/`
4. **Tailwind CSS** — estilização via classes utilitárias; CSS customizado em `app/assets/stylesheets/`
5. **Turbo Frames/Streams** — para atualizações parciais sem reload completo
6. **Importmap** — para adicionar pacotes JS, use `bin/importmap pin <pacote>`

---

## 10. Banco de Dados

### Convenções de Migração

```ruby
# ✅ Sempre adicione índices em foreign keys e colunas frequentemente consultadas
add_index :resources, :user_id
add_index :resources, :status
add_index :resources, [:category, :region]

# ✅ Use reversible quando possível
def change
  add_column :resources, :featured, :boolean, default: false, null: false
end
```

- Nunca edite `schema.rb` diretamente
- Use `null: false` e `default:` sempre que aplicável
- Adicione índices em foreign keys, colunas de busca e colunas de ordenação
- Use `bin/rails db:rollback` para desfazer e corrigir migrações com erro

### Bancos Solid (Produção)

O projeto usa bancos dedicados para infraestrutura em produção:

- `[app_name]_production` — banco principal
- `[app_name]_production_cache` — Solid Cache
- `[app_name]_production_queue` — Solid Queue
- `[app_name]_production_cable` — Solid Cable

---

## 11. Git

- Execute `bin/rubocop -a` antes de qualquer commit
- **Nunca** execute comandos destrutivos (`git restore`, `git reset`, `git clean`) sem permissão explícita
- Mantenha `.env` local (está no `.gitignore`)
- Use `.example.env` como template de variáveis de ambiente
- CI roda automaticamente em PRs e pushes para `main`

---

## 12. Anti-padrões — Não Faça

| # | Anti-padrão | Alternativa correta |
|---|-------------|---------------------|
| 1 | Editar `schema.rb` diretamente | Criar uma migração |
| 2 | Usar `and` / `or` | Usar `&&` / `||` |
| 3 | Esquecer de rodar lint/testes | Sempre: `bin/rubocop -a` → `bin/rails test` |
| 4 | Git destrutivo sem permissão | Perguntar ao usuário primeiro |
| 5 | JavaScript inline no HTML | Criar Stimulus controller |
| 6 | Adicionar gem sem verificar versão | Conferir versão compatível antes de `bundle add` |
| 7 | Colunas sem índice em queries frequentes | Adicionar índice na migração |
| 8 | Strings hardcoded em views | Usar i18n (`t('.chave')`) |
| 9 | Rescue genérico (`rescue => e`) | Rescue de exceção específica |
| 10 | Usar Sprockets/Webpack | Projeto usa Propshaft + Importmap |
| 11 | Instalar bundler JS (esbuild, webpack) | Usar Importmap: `bin/importmap pin <pkg>` |
| 12 | Usar `rails` diretamente | Sempre usar `bin/rails` |

---

## 13. Metodologia AI-SDD

O projeto segue a metodologia **AI-Specification-Driven Development**:

- **Documentação**: `ai-sdd/` contém PRD e techspec por fase
- **Comandos**: `.opencode/commands/` contém comandos de automação
- **Skills**: `.opencode/skills/` contém skills de automação (vision, product-map, PRD, roadmap, techspec, tasks, review)
- **MCP Servers**: GitHub MCP + Context7 configurados em `opencode.json`
