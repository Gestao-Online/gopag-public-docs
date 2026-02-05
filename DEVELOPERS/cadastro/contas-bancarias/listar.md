# Listar Contas Bancárias

Endpoint para listar todas as contas bancárias associadas a um vendedor específico.

## Endpoint

```
GET /v1/marketplaces/{marketplace_id}/sellers/{seller_id}/bank_accounts
```

## Parâmetros de URL

| Parâmetro | Tipo | Descrição |
|-----------|------|-----------|
| `marketplace_id` | string | ID do marketplace |
| `seller_id` | string | ID do vendedor |

## Response: 200 OK

```json
{
  "resource": "list",
  "uri": "/v1/marketplaces/abc123.../sellers/b9c9b3ca.../bank_accounts",
  "items": [
    {
      "id": "abc123def456789012345678901234ab",
      "resource": "bank_account",
      "holder_name": "TECH EXEMPLO COMPANY LTDA",
      "bank_code": "104",
      "bank_name": "Caixa Econômica Federal",
      "type": "checking",
      "last4_digits": "0664",
      "routing_number": "4928",
      "is_verified": true,
      "created_at": "2026-02-05T10:15:23+00:00"
    },
    {
      "id": "def456abc789012345678901234567cd",
      "resource": "bank_account",
      "holder_name": "TECH EXEMPLO COMPANY LTDA",
      "bank_code": "341",
      "bank_name": "Itaú Unibanco",
      "type": "savings",
      "last4_digits": "1234",
      "routing_number": "5678",
      "is_verified": false,
      "created_at": "2026-02-04T14:30:00+00:00"
    }
  ],
  "has_more": false,
  "limit": 100,
  "offset": 0,
  "total": 2
}
```

---

## Campos da Resposta

### Campos da Lista

| Campo | Tipo | Descrição |
|-------|------|-----------|
| `resource` | string | Tipo do recurso (`list`) |
| `uri` | string | URI da requisição |
| `items` | array | Array de contas bancárias |
| `has_more` | boolean | Indica se há mais itens disponíveis |
| `limit` | number | Limite de itens por página |
| `offset` | number | Offset atual da paginação |
| `total` | number | Total de contas do vendedor |

### Campos de Cada Conta

| Campo | Tipo | Descrição |
|-------|------|-----------|
| `id` | string | ID único da conta bancária |
| `resource` | string | Tipo do recurso (`bank_account`) |
| `holder_name` | string | Nome do titular da conta |
| `bank_code` | string | Código do banco (3 dígitos) |
| `bank_name` | string | Nome do banco |
| `type` | string | Tipo: `checking` ou `savings` |
| `last4_digits` | string | Últimos 4 dígitos da conta |
| `routing_number` | string | Número da agência |
| `is_verified` | boolean | Se a conta foi verificada |
| `created_at` | string | Data de criação (ISO 8601) |

> **Nota de Segurança**: O número completo da conta nunca é retornado, apenas os últimos 4 dígitos.

---

## Lista Vazia

Quando o vendedor não possui contas cadastradas:

```json
{
  "resource": "list",
  "uri": "/v1/marketplaces/abc123.../sellers/b9c9b3ca.../bank_accounts",
  "items": [],
  "has_more": false,
  "limit": 100,
  "offset": 0,
  "total": 0
}
```

## Boas Práticas

### ✅ Recomendações

1. **Cache inteligente**: Cache a lista por alguns minutos, contas bancárias não mudam frequentemente
2. **Verificar status**: Sempre cheque `is_verified` antes de processar pagamentos
3. **Ordenação**: Priorize contas verificadas em suas interfaces
4. **Múltiplas contas**: Permita que usuários escolham a conta de recebimento se houver mais de uma

---

## Próximos Passos

- [Criar nova conta bancária](criar.md)
- [Buscar detalhes de uma conta](detalhes.md)
- [Remover conta bancária](remover.md)

---

## Suporte

Precisa de ajuda?
- 📧 suporte@gopag.com.br
- 📚 [Documentação Completa](https://docs.gopag.com.br)
