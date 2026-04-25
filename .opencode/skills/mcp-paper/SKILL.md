---
name: mcp-paper
description: >-
  Referência completa do MCP Paper (paper.design). Documenta todas as 20 tools
  disponíveis, seus parâmetros, valores de retorno, padrões de uso, workflows
  compostos, troubleshooting e boas práticas. Use esta skill sempre que precisar
  interagir com o Paper Desktop via MCP — leitura de designs, criação de
  artboards, manipulação de nós, export de JSX/CSS/imagens.
  REQUER: Paper Desktop app rodando localmente (inicia o MCP server automaticamente).
  Use quando: qualquer interação com o Paper MCP for necessária.
  QUANDO NÃO: para lógica pura de Rails sem Paper (ver outras skills).
  RELAÇÃO: esta skill é referenciada por design-in-paper e paper-to-rails.
license: MIT
compatibility: Paper Desktop app + Paper MCP server (http://127.0.0.1:29979/mcp)
---

Você é um especialista no uso do MCP Paper. Esta skill é a referência canônica de todas
as tools expostas pelo Paper MCP server. Consulte-a sempre que precisar interagir com
o Paper Desktop via MCP — leitura, criação, manipulação e export de designs.

---

## Pré-requisitos

### Paper Desktop

O MCP server do Paper roda localmente e é iniciado automaticamente quando o Paper Desktop
está aberto com um arquivo. **Não existe binário separado** — basta abrir o app.

- **URL do server:** `http://127.0.0.1:29979/mcp`
- **Protocolo:** Streamable HTTP (MCP — Model Context Protocol)
- **Autenticação:** nenhuma (local only)

### Verificação de Conexão

Antes de qualquer operação, **sempre chame `get_basic_info`**.
Se falhar, oriente o usuário:

1. Verifique se o Paper Desktop está aberto
2. Verifique se há um arquivo aberto no app
3. Consulte: https://paper.design/docs/mcp
4. Se Windows WSL: habilite [mirrored mode networking](https://learn.microsoft.com/en-us/windows/wsl/networking#mirrored-mode-networking)

**Não prossiga sem confirmação de conexão.**

### Configuração por Editor

| Editor | Configuração |
|--------|-------------|
| **Antigravity** | `mcp_config.json`: `{ "mcpServers": { "paper": { "serverUrl": "http://127.0.0.1:29979/mcp" } } }` |
| **Cursor** | `/add-plugin paper-desktop` ou Cursor Settings > Tools & MCP |
| **Claude Code** | `/plugin marketplace add paper-design/agent-plugins` → `/plugin install paper-desktop@paper` |
| **Codex** | Settings > MCP Servers > Streamable HTTP, nome `paper`, URL `http://127.0.0.1:29979/mcp` |
| **Copilot (VS Code)** | `.vscode/mcp.json`: `{ "servers": { "paper": { "type": "http", "url": "http://127.0.0.1:29979/mcp" } } }` |
| **OpenCode** | `opencode.json`: `{ "mcp": { "paper": { "type": "remote", "url": "http://127.0.0.1:29979/mcp", "enabled": true } } }` |

---

## Referência Completa das Tools

As tools são organizadas em 3 categorias:

1. **Leitura** (read-only) — inspecionar design, capturar screenshots, export
2. **Escrita** (write) — criar/modificar/deletar nós e artboards
3. **Indicadores** (indicators) — feedback visual durante trabalho em progresso

---

### 1. Tools de Leitura

#### `get_basic_info`

Retorna informações básicas do arquivo aberto no Paper.

| Campo | Detalhe |
|-------|---------|
| **Parâmetros** | Nenhum |
| **Retorno** | Nome do arquivo, nome da página, contagem de nós, lista de artboards com dimensões (largura × altura) |
| **Uso típico** | Verificação de conexão; descobrir artboards disponíveis; obter IDs de artboards |
| **Quando usar** | Sempre como primeiro passo de qualquer workflow |

```
Exemplo de uso:
→ get_basic_info()
← { fileName: "landing-page.paper", pageName: "Page 1", nodeCount: 142,
    artboards: [{ id: "abc123", name: "Hero / desktop", width: 1440, height: 900 }, ...] }
```

---

#### `get_selection`

Retorna detalhes sobre os nós atualmente selecionados pelo usuário no Paper.

| Campo | Detalhe |
|-------|---------|
| **Parâmetros** | Nenhum |
| **Retorno** | IDs, nomes, tipos, tamanho (width/height) e artboard pai de cada nó selecionado |
| **Uso típico** | Contexto baseado na seleção do usuário; ponto de partida para inspecção |
| **Quando usar** | Quando o usuário diz "use o que está selecionado", "implemente isso" |

```
Exemplo de uso:
→ get_selection()
← { nodes: [{ id: "xyz789", name: "hero-section", type: "Frame", width: 1440, height: 600,
              artboard: { id: "abc123", name: "Hero / desktop" } }] }
```

**Dica:** Peça ao usuário para selecionar o nó desejado antes de chamar esta tool.

---

#### `get_node_info`

Retorna informações detalhadas de um nó específico por ID.

| Campo | Detalhe |
|-------|---------|
| **Parâmetros** | `id` (string) — ID do nó |
| **Retorno** | Tamanho, visibilidade, estado de lock, nó pai, filhos, conteúdo de texto (se Text node) |
| **Uso típico** | Inspecção profunda de um nó; verificar propriedades; ler texto |
| **Quando usar** | Após obter um ID via `get_basic_info`, `get_selection`, ou `get_children` |

```
Exemplo de uso:
→ get_node_info({ id: "xyz789" })
← { id: "xyz789", name: "hero-title", type: "Text", width: 600, height: 48,
    visible: true, locked: false, parent: { id: "abc456" },
    children: [], textContent: "Build Something Beautiful" }
```

---

#### `get_children`

Retorna os filhos diretos de um nó.

| Campo | Detalhe |
|-------|---------|
| **Parâmetros** | `id` (string) — ID do nó pai |
| **Retorno** | Lista de filhos diretos com: IDs, nomes, tipos, contagem de filhos (recursiva) |
| **Uso típico** | Explorar hierarquia nível a nível; entender composição de um frame |
| **Quando usar** | Navegação seletiva quando `get_tree_summary` retorna muita informação |

```
Exemplo de uso:
→ get_children({ id: "abc123" })
← { children: [
    { id: "ch1", name: "header", type: "Frame", childCount: 5 },
    { id: "ch2", name: "content", type: "Frame", childCount: 12 },
    { id: "ch3", name: "footer", type: "Frame", childCount: 3 }
  ] }
```

---

#### `get_tree_summary`

Retorna um resumo textual compacto da hierarquia de subárvore de um nó.

| Campo | Detalhe |
|-------|---------|
| **Parâmetros** | `id` (string) — ID do nó raiz; `depth` (number, opcional) — limite de profundidade |
| **Retorno** | Resumo textual indentado mostrando hierarquia (nomes, tipos, nesting) |
| **Uso típico** | Visão geral da estrutura; entender organização do design antes de iteração |
| **Quando usar** | Antes de `get_jsx` para decidir quais nós converter; em artboards grandes, use `depth` para limitar |

```
Exemplo de uso:
→ get_tree_summary({ id: "abc123", depth: 3 })
← "Frame 'Hero / desktop' (1440×900)
     Frame 'header' (1440×80)
       Text 'logo' — 'Paper'
       Frame 'nav' (3 children)
     Frame 'content' (1440×600)
       Text 'title' — 'Build Something Beautiful'
       Text 'subtitle' — 'Design meets code.'
       Frame 'cta-buttons' (2 children)
     Frame 'footer' (1440×220)"
```

**Boas práticas:**
- Use `depth: 2` ou `depth: 3` para artboards grandes para evitar output excessivo
- Sem `depth`, retorna a árvore completa

---

#### `get_screenshot`

Captura um screenshot de um nó específico.

| Campo | Detalhe |
|-------|---------|
| **Parâmetros** | `id` (string) — ID do nó; `scale` (number, opcional) — `1` (padrão) ou `2` para 2x |
| **Retorno** | Imagem em base64 (PNG) |
| **Uso típico** | Verificação visual; comparar resultado antes/depois; referência para implementação |
| **Quando usar** | Após criar/modificar artboards; durante `paper-to-rails` para referência visual |

```
Exemplo de uso:
→ get_screenshot({ id: "abc123", scale: 2 })
← { image: "data:image/png;base64,iVBOR..." }
```

**Boas práticas:**
- Use `scale: 1` para visão geral (menor payload)
- Use `scale: 2` para detalhes finos (tipografia, ícones)
- Capture antes e depois para validar alterações

---

#### `get_jsx`

Retorna o JSX de um nó e todos os seus descendentes.

| Campo | Detalhe |
|-------|---------|
| **Parâmetros** | `id` (string) — ID do nó; `format` (string, opcional) — `"tailwind"` (padrão) ou `"inline-styles"` |
| **Retorno** | Código JSX com classes Tailwind ou estilos inline, representando a árvore visual |
| **Uso típico** | Conversão design → código; base para templates ERB; entender layout e estilos |
| **Quando usar** | Principal tool da skill `paper-to-rails`; quando precisa do código do design |

```
Exemplo de uso:
→ get_jsx({ id: "abc123", format: "tailwind" })
← '<div className="flex flex-col gap-6 p-8 bg-white">
     <h1 className="text-2xl font-semibold text-gray-900">Build Something Beautiful</h1>
     <p className="text-base text-gray-500">Design meets code.</p>
   </div>'
```

**Boas práticas:**
- Use `format: "tailwind"` para projetos Tailwind (padrão do projeto)
- Use `format: "inline-styles"` quando precisar de CSS raw ou para `design-in-paper`
- O JSX retornado usa `className` — converter para `class` no ERB
- Textos hardcoded devem ser substituídos por `t('.chave')` na conversão

---

#### `get_computed_styles`

Retorna CSS computado de um ou mais nós (operação em batch).

| Campo | Detalhe |
|-------|---------|
| **Parâmetros** | `ids` (string[]) — array de IDs dos nós |
| **Retorno** | Mapa de ID → estilos CSS computados (propriedades e valores resolvidos) |
| **Uso típico** | Extrair valores exatos de design tokens; verificar cores, fontes, espaçamentos |
| **Quando usar** | Complemento ao `get_jsx` para valores precisos; mapear para tokens do design system |

```
Exemplo de uso:
→ get_computed_styles({ ids: ["xyz789", "xyz790"] })
← { "xyz789": { "font-family": "Inter", "font-size": "24px", "color": "#1a1a1a",
                 "display": "flex", "gap": "16px" },
    "xyz790": { "background-color": "#3b82f6", "border-radius": "12px", "padding": "12px 24px" } }
```

**Boas práticas:**
- Passe múltiplos IDs numa única chamada para eficiência (batch)
- Use para mapear cores hardcoded → tokens do design system

---

#### `get_fill_image`

Retorna a imagem de preenchimento (fill) de um nó que possui image fill.

| Campo | Detalhe |
|-------|---------|
| **Parâmetros** | `id` (string) — ID do nó com image fill |
| **Retorno** | Imagem em base64 (JPEG) |
| **Uso típico** | Extrair imagens usadas no design; salvar assets para o projeto |
| **Quando usar** | Quando um nó contém uma imagem e precisa extraí-la para uso no código |

```
Exemplo de uso:
→ get_fill_image({ id: "img001" })
← { image: "data:image/jpeg;base64,/9j/4AAQ..." }
```

---

#### `get_font_family_info`

Verifica a disponibilidade de uma família de fontes.

| Campo | Detalhe |
|-------|---------|
| **Parâmetros** | `family` (string) — nome da família de fontes |
| **Retorno** | Se está disponível (máquina local ou Google Fonts); pesos e estilos disponíveis |
| **Uso típico** | Verificar se fonte do design está acessível; decidir fallbacks |
| **Quando usar** | Durante `paper-to-rails` para garantir que fontes do design estarão disponíveis no projeto |

```
Exemplo de uso:
→ get_font_family_info({ family: "Inter" })
← { available: true, source: "google-fonts",
    weights: [100, 200, 300, 400, 500, 600, 700, 800, 900],
    styles: ["normal", "italic"] }
```

---

#### `get_guide`

Recupera workflows guiados para tópicos específicos.

| Campo | Detalhe |
|-------|---------|
| **Parâmetros** | `topic` (string) — tópico do guia (ex: `"figma-import"`) |
| **Retorno** | Passos guiados para o tópico solicitado |
| **Uso típico** | Obter instruções oficiais do Paper para workflows específicos |
| **Quando usar** | Quando precisar de orientação sobre importação Figma, setup, ou outros workflows |

```
Exemplo de uso:
→ get_guide({ topic: "figma-import" })
← { steps: ["1. Open your Figma file...", "2. Select the frame...", ...] }
```

**Tópicos conhecidos:**
- `figma-import` — passos para importar designs do Figma

---

### 2. Tools de Escrita

#### `find_placement`

Sugere coordenadas x/y para posicionar um novo artboard sem sobreposição.

| Campo | Detalhe |
|-------|---------|
| **Parâmetros** | Nenhum (ou dimensões opcionais do artboard planejado) |
| **Retorno** | Coordenadas `x`, `y` sugeridas para posicionamento |
| **Uso típico** | Encontrar espaço livre no canvas antes de criar um artboard |
| **Quando usar** | **Sempre antes de `create_artboard`** — evita sobreposição |

```
Exemplo de uso:
→ find_placement()
← { x: 1600, y: 0 }
```

---

#### `create_artboard`

Cria um novo artboard no canvas.

| Campo | Detalhe |
|-------|---------|
| **Parâmetros** | `name` (string, opcional) — nome do artboard; `styles` (object, opcional) — width, height e outros estilos CSS |
| **Retorno** | ID do artboard criado |
| **Uso típico** | Criar frames para telas de design; base para escrever HTML |
| **Quando usar** | Início do workflow de design; criação de novos artboards |

```
Exemplo de uso:
→ create_artboard({ name: "orders / Lista / desktop", styles: { width: 1440, height: 900 } })
← { id: "new_abc123" }
```

**Nomenclatura padrão do projeto:**
```
[feature-slug] / [Nome da Tela] / [breakpoint]
```
Exemplos:
- `orders / Lista de Pedidos / desktop`
- `orders / Formulário / mobile`
- `auth / Login / desktop`

---

#### `write_html`

Parseia HTML e adiciona ou substitui nós no Paper.

| Campo | Detalhe |
|-------|---------|
| **Parâmetros** | `id` (string) — ID do nó alvo; `html` (string) — HTML para adicionar; `mode` (string) — `"insert-children"` ou `"replace"` |
| **Retorno** | IDs dos nós criados |
| **Uso típico** | Inserir layout HTML estruturado num artboard; popular artboard com conteúdo |
| **Quando usar** | Após `create_artboard` para popular o artboard; para atualizar conteúdo existente |

```
Exemplo de uso:
→ write_html({
    id: "new_abc123",
    mode: "insert-children",
    html: '<div style="display: flex; flex-direction: column; gap: 24px; padding: 32px;">
             <h1 style="font-size: 24px; font-weight: 600;">Lista de Pedidos</h1>
           </div>'
  })
← { nodeIds: ["node_001", "node_002"] }
```

**Modos:**
- `"insert-children"` — adiciona os nós HTML como filhos do nó alvo (não remove existentes)
- `"replace"` — substitui o conteúdo inteiro do nó alvo pelo novo HTML

**Boas práticas para o HTML:**
- Use **flex layouts** (não grid complexo) — melhor compatibilidade
- Use **estilos inline** (`style=`) — o Paper interpreta CSS inline
- Use **CSS Variables** quando possível para tokens do design system
- Use **conteúdo real** — não Lorem Ipsum
- Estruture semanticamente — cada seção lógica é um partial potencial em Rails

---

#### `set_text_content`

Define o conteúdo de texto de um ou mais Text nodes (batch).

| Campo | Detalhe |
|-------|---------|
| **Parâmetros** | `updates` (array) — `[{ id: string, text: string }]` |
| **Retorno** | Confirmação dos nós atualizados |
| **Uso típico** | Atualizar textos em massa; sincronizar conteúdo real; traduzir textos |
| **Quando usar** | Substituir placeholder text; atualizar conteúdo de Notion/API; traduções |

```
Exemplo de uso:
→ set_text_content({ updates: [
    { id: "text_001", text: "Gerencie seus pedidos" },
    { id: "text_002", text: "Crie, edite e acompanhe todos os pedidos da plataforma." }
  ] })
← { updated: ["text_001", "text_002"] }
```

---

#### `rename_nodes`

Renomeia uma ou mais layers (batch).

| Campo | Detalhe |
|-------|---------|
| **Parâmetros** | `updates` (array) — `[{ id: string, name: string }]` |
| **Retorno** | Confirmação dos nós renomeados |
| **Uso típico** | Organizar layers com nomes semânticos; preparar estrutura para conversão Rails |
| **Quando usar** | Após `write_html` para nomear layers semanticamente |

```
Exemplo de uso:
→ rename_nodes({ updates: [
    { id: "node_001", name: "page-header" },
    { id: "node_002", name: "order-table" },
    { id: "node_003", name: "empty-state" }
  ] })
← { renamed: ["node_001", "node_002", "node_003"] }
```

**Nomes semânticos padrão Rails:**
- `page-header`, `page-content`, `page-footer`
- `card-list`, `card-item`, `card-detail`
- `form-section`, `form-actions`
- `modal-overlay`, `modal-panel`
- `empty-state`, `loading-state`
- `sidebar-nav`, `breadcrumb`

---

#### `duplicate_nodes`

Clona nós profundamente (deep clone).

| Campo | Detalhe |
|-------|---------|
| **Parâmetros** | `ids` (string[]) — IDs dos nós para clonar |
| **Retorno** | Novos IDs e mapa de IDs descendentes (old → new) |
| **Uso típico** | Copiar componentes; criar variações de um design existente |
| **Quando usar** | Quando precisa de versões mobile/tablet de um artboard desktop; duplicar componentes |

```
Exemplo de uso:
→ duplicate_nodes({ ids: ["abc123"] })
← { newIds: ["dup_abc123"],
    descendantMap: { "abc123": "dup_abc123", "ch1": "dup_ch1", "ch2": "dup_ch2" } }
```

---

#### `update_styles`

Atualiza estilos CSS de um ou mais nós.

| Campo | Detalhe |
|-------|---------|
| **Parâmetros** | `updates` (array) — `[{ id: string, styles: object }]` |
| **Retorno** | Confirmação dos nós atualizados |
| **Uso típico** | Ajustar cores, tamanhos, espaçamentos; aplicar design tokens |
| **Quando usar** | Refinamento visual; aplicar nova paleta; ajustar responsive |

```
Exemplo de uso:
→ update_styles({ updates: [
    { id: "node_001", styles: { "background-color": "#1e293b", "padding": "24px 32px" } },
    { id: "node_002", styles: { "font-size": "18px", "color": "#94a3b8" } }
  ] })
← { updated: ["node_001", "node_002"] }
```

---

#### `delete_nodes`

Deleta um ou mais nós e todos os seus descendentes.

| Campo | Detalhe |
|-------|---------|
| **Parâmetros** | `ids` (string[]) — IDs dos nós para deletar |
| **Retorno** | Confirmação dos nós deletados |
| **Uso típico** | Remover artboards obsoletos; limpar nós indesejados |
| **Quando usar** | Com cuidado — operação destrutiva e irreversível |

```
Exemplo de uso:
→ delete_nodes({ ids: ["old_artboard_001"] })
← { deleted: ["old_artboard_001"] }
```

**⚠️ CUIDADO:** Esta operação é irreversível. Sempre confirme com o usuário antes de deletar.

---

### 3. Tools de Indicadores

#### `start_working_on_nodes`

Marca artboards como "em progresso", mostrando um indicador visual no Paper.

| Campo | Detalhe |
|-------|---------|
| **Parâmetros** | `ids` (string[]) — IDs dos artboards |
| **Retorno** | Confirmação |
| **Uso típico** | Feedback visual para o usuário de que o agente está trabalhando |
| **Quando usar** | No início de operações de criação/modificação de artboards |

```
Exemplo de uso:
→ start_working_on_nodes({ ids: ["abc123"] })
← { working: ["abc123"] }
```

---

#### `finish_working_on_nodes`

Remove o indicador de "em progresso" dos artboards.

| Campo | Detalhe |
|-------|---------|
| **Parâmetros** | `ids` (string[]) — IDs dos artboards |
| **Retorno** | Confirmação |
| **Uso típico** | Sinalizar que a operação foi concluída |
| **Quando usar** | Após concluir criação/modificação; **sempre chamar** para limpar o indicador |

```
Exemplo de uso:
→ finish_working_on_nodes({ ids: ["abc123"] })
← { finished: ["abc123"] }
```

**Importante:** Sempre chame `finish_working_on_nodes` após `start_working_on_nodes`,
mesmo em caso de erro — caso contrário o indicador ficará preso.

---

## Workflows Compostos

### Workflow 1: Inspecionar um Design Existente

```
1. get_basic_info()                          → verificar conexão + listar artboards
2. get_selection()                           → ver se há seleção ativa
   OU identificar artboard por nome da lista
3. get_tree_summary(id, depth: 3)            → entender hierarquia
4. get_screenshot(id, scale: 1)              → capturar visual
5. get_jsx(id, format: "tailwind")           → obter código do design
6. get_computed_styles(ids[])                → CSS exato dos nós principais
7. get_font_family_info(family)              → verificar disponibilidade de fontes
```

### Workflow 2: Criar um Artboard Novo

```
1. get_basic_info()                          → verificar conexão
2. find_placement()                          → coordenadas sem sobreposição
3. create_artboard(name, styles)             → criar o artboard
4. start_working_on_nodes([id])              → indicador visual
5. write_html(id, html, "insert-children")   → popular com conteúdo
6. rename_nodes(updates)                     → nomes semânticos nas layers
7. get_screenshot(id, scale: 1)              → verificar resultado
8. finish_working_on_nodes([id])             → remover indicador
```

### Workflow 3: Modificar um Artboard Existente

```
1. get_basic_info()                          → verificar conexão
2. get_selection() OU get_node_info(id)      → localizar nó alvo
3. start_working_on_nodes([artboardId])      → indicador visual
4. get_children(id)                          → entender estrutura atual
5. write_html(id, html, "replace")           → substituir conteúdo
   OU update_styles(updates)                → ajustar estilos
   OU set_text_content(updates)             → atualizar textos
6. get_screenshot(id, scale: 1)              → verificar resultado
7. finish_working_on_nodes([artboardId])     → remover indicador
```

### Workflow 4: Converter Design → Código (usado por paper-to-rails)

```
1. get_basic_info()                          → verificar conexão
2. get_selection()                           → artboard selecionado
3. get_tree_summary(id, depth: 3)            → hierarquia para planejamento
4. get_screenshot(id, scale: 2)              → referência visual detalhada
5. get_jsx(id, format: "tailwind")           → código base para conversão
6. get_computed_styles(ids[])                → valores exatos para tokens
7. get_fill_image(id)                        → extrair imagens usadas
8. get_font_family_info(family)              → verificar fontes
→ Converter JSX → ERB + Tailwind + i18n (ver skill paper-to-rails)
```

### Workflow 5: Duplicar para Breakpoint Responsivo

```
1. get_basic_info()                          → listar artboards existentes
2. duplicate_nodes([desktopArtboardId])      → clonar artboard desktop
3. rename_nodes([{ id: newId, name: "feature / Tela / mobile" }])
4. update_styles([{ id: newId, styles: { width: 390, height: 844 } }])
5. start_working_on_nodes([newId])
6. (ajustar layout interno para mobile — update_styles nos filhos)
7. get_screenshot(newId, scale: 1)           → verificar resultado
8. finish_working_on_nodes([newId])
```

### Workflow 6: Sincronizar Conteúdo Real

```
1. get_basic_info()                          → verificar conexão
2. get_tree_summary(artboardId, depth: 3)    → identificar Text nodes
3. get_children(artboardId)                  → obter IDs dos nós de texto
4. set_text_content([                        → atualizar em batch
     { id: "text_01", text: "Conteúdo real da API/DB" },
     { id: "text_02", text: "Outro conteúdo real" }
   ])
5. get_screenshot(artboardId)                → verificar resultado
```

---

## Boas Práticas

### Performance

- **Batch sempre que possível**: `get_computed_styles`, `set_text_content`, `rename_nodes`,
  `update_styles` aceitam múltiplos IDs — use uma chamada com array em vez de múltiplas chamadas
- **Limite profundidade**: Use `depth` em `get_tree_summary` para artboards grandes
- **Scale 1x primeiro**: Use `scale: 1` em `get_screenshot` para verificação rápida; `2` apenas quando necessário

### Robustez

- **Verifique conexão**: Sempre comece com `get_basic_info()`
- **Indicadores**: Sempre pareie `start_working_on_nodes` com `finish_working_on_nodes`
- **Verificação visual**: Capture `get_screenshot` após cada operação de escrita
- **Confirme antes de deletar**: `delete_nodes` é irreversível — peça confirmação ao usuário

### Estrutura de Design (para conversão Rails)

- Use **flex layouts** (não grid CSS complexo)
- Use **estilos inline** no HTML escrito via `write_html`
- Use **conteúdo real** — nunca Lorem Ipsum
- Nomeie layers com `rename_nodes` usando **nomes semânticos Rails**
- Estruture como partials potenciais: cada seção lógica = um partial

### Nomenclatura de Artboards

```
[feature-slug] / [Nome da Tela] / [breakpoint]
```

Breakpoints padrão:
| Breakpoint | Dimensões | Uso |
|-----------|-----------|-----|
| `desktop` | 1440 × 900 | Principal |
| `tablet` | 768 × 1024 | Opcional |
| `mobile` | 390 × 844 | Responsivo |

---

## Troubleshooting

| Problema | Causa Provável | Solução |
|----------|---------------|---------|
| `get_basic_info` falha | Paper Desktop não está aberto | Abrir o app com um arquivo |
| Tools retornam erro de conexão | MCP server não iniciou | Reabrir arquivo no Paper Desktop |
| Agente "não vê" as tools | Sessão longa/corrompida | Reiniciar sessão do agente |
| Tool call com parâmetros errados | Hallucination do LLM | Reiniciar sessão; usar esta referência |
| Artboards sobrepostos | Não usou `find_placement` | Sempre chamar antes de `create_artboard` |
| Indicador "working" preso | `finish_working_on_nodes` não chamado | Chamar manualmente com os IDs |
| Windows WSL: conexão recusada | Rede WSL isolada | Habilitar mirrored mode networking |
| Limites não resetados após upgrade | Bug em versões antigas | Atualizar Paper Desktop: About > Check for updates |

---

## Relação com Outras Skills

| Skill | Relação |
|-------|---------|
| `design-in-paper` | **Usa esta skill** — cria artboards a partir de PRDs |
| `paper-to-rails` | **Usa esta skill** — lê designs e converte para ERB |
| `tailwind-design-system` | Fornece tokens para `write_html` e mapeamento em `get_computed_styles` |
| `frontend-design` | Princípios de design aplicados na criação de artboards |
| `rails-visual-design` | Fallback quando Paper MCP não está disponível |
| `executa-task` | Aciona `paper-to-rails` que usa esta skill indiretamente |

---

## Links de Referência

- **Documentação oficial:** https://paper.design/docs/mcp
- **Downloads:** https://paper.design/downloads
- **Troubleshooting:** https://paper.design/docs/mcp#troubleshooting
- **Changelog:** https://paper.design/build-log
