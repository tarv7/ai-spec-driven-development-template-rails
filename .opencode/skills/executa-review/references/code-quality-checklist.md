# Checklist de Qualidade de Código

> Este checklist complementa o `AGENTS.md`. As regras do projeto (convenções Rails, i18n, nomenclatura, formatação) já estão definidas lá. Aqui ficam apenas os critérios de **verificação e inspeção** que exigem análise do código pronto.

## Design e Estrutura

| Aspecto | O que verificar | Prioridade |
|---------|-----------------|------------|
| Complexidade | Funções curtas e focadas, baixa complexidade ciclomática, poucos níveis de aninhamento | 🔴 Alta |
| DRY | Sem código duplicado — extrair para concerns, helpers ou services quando repetir | 🔴 Alta |
| SOLID | Responsabilidade única por classe/método, dependências injetáveis, interfaces coesas | 🟡 Média |
| Acoplamento | Componentes com baixo acoplamento — mudanças em um não quebram outro | 🟡 Média |

## Segurança

| Aspecto | O que verificar | Prioridade |
|---------|-----------------|------------|
| SQL Injection | Queries parametrizadas, sem interpolação de strings em SQL | 🔴 Alta |
| XSS | Saídas sanitizadas, uso correto de `html_safe` / `raw` | 🔴 Alta |
| Mass Assignment | Strong parameters filtrando todos os atributos em controllers | 🔴 Alta |
| Autenticação | Rotas protegidas, `before_action :authenticate` onde necessário | 🔴 Alta |
| Dados Sensíveis | Sem segredos hardcoded, logs não expõem dados pessoais | 🟡 Média |

## Performance

| Aspecto | O que verificar | Prioridade |
|---------|-----------------|------------|
| N+1 Queries | Usar `includes`/`eager_load` para evitar queries em loop | 🔴 Alta |
| Índices | Colunas usadas em `where`, `order`, `joins` e foreign keys têm índices | 🟡 Média |
| Queries Pesadas | Sem `SELECT *` desnecessário, paginação em listagens grandes | 🟡 Média |
| Caching | Fragmentos de view frequentes e estáveis usam cache quando aplicável | 🟢 Baixa |

## Testes

| Aspecto | O que verificar | Prioridade |
|---------|-----------------|------------|
| Cobertura | Funcionalidades novas têm testes unitários e de integração | 🔴 Alta |
| Significância | Testes validam comportamento de negócio, não apenas cobertura numérica | 🔴 Alta |
| Edge Cases | Cenários de erro, limites e entradas inválidas testados | 🟡 Média |
| Isolamento | Testes não dependem de ordem de execução ou estado compartilhado | 🟡 Média |
