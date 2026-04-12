# Tech Spec - [Nome da Funcionalidade]

## Resumo Executivo

[Breve visão técnica da abordagem de solução em 1-2 parágrafos:
- Decisões arquiteturais principais
- Estratégia de implementação
- Referência ao PRD correspondente]

## Arquitetura do Sistema

### Visão Geral dos Componentes

[Descrição dos componentes principais e suas responsabilidades:

- **[Componente 1]**: [Responsabilidade] — Novo / Modificado
- **[Componente 2]**: [Responsabilidade] — Novo / Modificado

Inclua TODOS os componentes novos ou que serão modificados.

Relacionamentos principais entre componentes e visão geral do fluxo de dados.]

## Design de Implementação

### Interfaces Principais

[Defina interfaces de serviço principais (≤20 linhas por exemplo):

```ruby
# Exemplo — adapte à linguagem do projeto
class NomeServico
  def nome_metodo(parametro)
    # Descrição do comportamento esperado
  end
end
```

]

### Modelos de Dados

[Defina estruturas de dados essenciais:

- **Entidades de domínio**: Modelos ActiveRecord com atributos principais e relacionamentos
- **Migrações**: Tabelas a criar/modificar, colunas, índices, foreign keys
- **Validações**: Regras de validação e constraints do banco

Exemplo:
```ruby
# resources
# - name: string, not null
# - price: decimal(10,2)
# - resource_type: enum (type_a, type_b, type_c)
#
# relationships
# - belongs_to :user
# - has_many :images
#
# indexes
# - [:resource_type, :status]
```
]

### Endpoints / Rotas

[Liste rotas e ações de controllers se aplicável:

| Método | Rota | Controller#Action | Descrição |
|--------|------|-------------------|-----------|
| GET | `/recurso` | `recursos#index` | Listagem |
| POST | `/recurso` | `recursos#create` | Criação |

Referencie requisitos do PRD (RF-XXX) quando aplicável.]

## Pontos de Integração

[Inclua apenas se a funcionalidade requer integrações externas:

- **Serviço/API**: [Nome] — [Propósito]
- **Autenticação**: [Método de autenticação]
- **Tratamento de Erros**: [Abordagem para falhas e retries]
- **Fallback**: [Comportamento quando integração está indisponível]]

## Abordagem de Testes

### Testes Unitários

[Estratégia de testes unitários (Minitest):

- **Modelos**: Validações, scopes, métodos de negócio
- **Services**: Lógica de negócio isolada
- **Cenários críticos**: [Liste os mais importantes]
- **Mocks**: Apenas para serviços externos]

### Testes de Integração

[Testes de controllers e integração:

- **Controllers**: Respostas, redirecionamentos, strong parameters
- **Fluxos**: Request specs testando fluxos completos
- **Dados de teste**: Fixtures necessárias]

### Testes E2E

[Testes de sistema com Capybara:

- **Fluxos principais**: [Liste os fluxos happy path]
- **Casos extremos**: [Cenários de erro importantes]
- **Acessibilidade**: Verificações básicas de acessibilidade]

## Sequenciamento de Desenvolvimento

### Ordem de Construção

[Defina sequência de implementação respeitando dependências:

1. **[Componente]**: [Justificativa de porque primeiro] — Ref: RF-XXX
2. **[Componente]**: [Dependências que devem estar prontas]
3. **[Componente]**: [O que precisa estar pronto antes]
4. **Integração e Testes**: [Validação final]]

### Dependências Técnicas

[Liste dependências bloqueantes:

- **Gems/Bibliotecas**: [Nome] — [Versão] — [Propósito]
- **Infraestrutura**: [O que precisa estar configurado]
- **Serviços externos**: [Disponibilidade requerida]]

## Monitoramento e Observabilidade

[Defina abordagem de monitoramento:

- **Logs**: Eventos principais a registrar e níveis (info, warn, error)
- **Métricas**: Indicadores de saúde da funcionalidade
- **Alertas**: Condições que requerem atenção]

## Considerações Técnicas

### Decisões Principais

[Documente decisões técnicas importantes:

| Decisão | Escolha | Justificativa | Alternativas Rejeitadas |
|---------|---------|---------------|------------------------|
| [Área] | [Escolha] | [Por quê] | [O que foi considerado] |
]

### Riscos Conhecidos

[Identifique riscos técnicos:

| Risco | Probabilidade | Impacto | Mitigação |
|-------|--------------|---------|-----------| 
| [Risco] | Alta/Média/Baixa | Alto/Médio/Baixo | [Ação] |
]

### Conformidade com Standards do Projeto

[Skills e padrões do projeto que se aplicam a esta spec:

- **[Skill/Padrão]**: [Como esta spec está conforme] ou [Desvio: justificativa e alternativa]
- **AGENTS.md**: [Conformidade com convenções Rails, nomenclatura, i18n, etc.]]

### Arquivos Relevantes e Dependentes

[Arquivos existentes que serão impactados ou consultados:

- `[caminho/arquivo]` — [Tipo de impacto: novo, modificado, consultado]
- `[caminho/arquivo]` — [Tipo de impacto]]
