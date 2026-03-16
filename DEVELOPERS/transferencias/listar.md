# Listar Transferências

Lista todas as transferências realizadas para um vendedor específico.

## Endpoint

```
GET /v1/marketplaces/{marketplace_id}/sellers/{seller_id}/transfers
```

## Parâmetros de URL

| Parâmetro | Tipo | Obrigatório | Descrição |
|-----------|------|-------------|-----------|
| `marketplace_id` | string | Sim | ID do marketplace |
| `seller_id` | string | Sim | ID do vendedor |

## Parâmetros de Query (Opcionais)

### Paginação

| Parâmetro | Tipo | Descrição | Padrão |
|-----------|------|-----------|--------|
| `limit` | integer | Número de itens por página (1-100) | 20 |
| `offset` | integer | Número de itens para pular | 0 |
| `page` | integer | Número da página (alternativa ao offset) | 1 |

### Ordenação

| Parâmetro | Tipo | Descrição | Exemplo |
|-----------|------|-----------|---------|
| `sort` | string | Ordenação dos resultados | `time-descending`, `time-ascending`, `amount:asc`, `status:desc` |

### Filtros

| Parâmetro | Tipo | Descrição | Exemplo |
|-----------|------|-----------|---------|
| `status` | string | Status da transferência (separados por vírgula) | `succeeded`, `pending,failed` |
| `date_range[gte]` | string | Data maior ou igual (ISO 8601) | `2024-01-01T00:00:00Z` |
| `date_range[lte]` | string | Data menor ou igual (ISO 8601) | `2024-12-31T23:59:59Z` |

## Headers

```
Authorization: Bearer {api_token}
Accept: application/json
```

## Exemplo de Requisição

```bash
curl --request GET \
  --url 'https://api.gopag.com.br/v1/marketplaces/abc123.../sellers/def456.../transfers?limit=10&status=succeeded&sort=time-descending' \
  --header 'Authorization: Bearer seu_token_aqui' \
  --header 'Accept: application/json'
```

## Exemplo de Resposta (200 OK)

```json
{
  "resource": "list",
  "uri": "/v1/marketplaces/abc123.../sellers/def456.../transfers",
  "items": [
    {
      "id": "transfer123...",
      "resource": "transfer",
      "amount": 50000,
      "original_amount": 50000,
      "currency": "BRL",
      "status": "succeeded",
      "description": "Transferência automática - Semana 03/2024",
      "recipient": "def456...",
      "sender": "abc123...",
      "bank_account": {
        "id": "bank789...",
        "resource": "bank_account",
        "account_number": "12345",
        "routing_number": "001",
        "bank_code": "001",
        "holder_name": "João Silva Comercio LTDA",
        "type": "checking"
      },
      "transfer_date": "2024-01-15",
      "transfer_number": "TRF-2024-00123",
      "type": "automatic",
      "confirmed": true,
      "created_at": "2024-01-15T10:00:00Z",
      "updated_at": "2024-01-15T10:05:00Z"
    },
    {
      "id": "transfer124...",
      "resource": "transfer",
      "amount": 30000,
      "original_amount": 30000,
      "currency": "BRL",
      "status": "pending",
      "description": "Transferência automática - Semana 02/2024",
      "recipient": "def456...",
      "sender": "abc123...",
      "bank_account": {
        "id": "bank789...",
        "resource": "bank_account",
        "account_number": "12345",
        "routing_number": "001",
        "bank_code": "001",
        "holder_name": "João Silva Comercio LTDA",
        "type": "checking"
      },
      "transfer_date": "2024-01-08",
      "transfer_number": "TRF-2024-00122",
      "type": "automatic",
      "confirmed": false,
      "created_at": "2024-01-08T10:00:00Z",
      "updated_at": "2024-01-08T10:02:00Z"
    }
  ],
  "total": 15,
  "total_pages": 2,
  "page": 1,
  "limit": 10,
  "offset": 0,
  "has_more": true
}
```

## Campos da Resposta

### Campos de Paginação

| Campo | Tipo | Descrição |
|-------|------|-----------|
| `resource` | string | Tipo do recurso (sempre "list") |
| `uri` | string | URI da requisição |
| `items` | array | Array de transferências |
| `total` | integer | Total de transferências encontradas |
| `total_pages` | integer | Total de páginas disponíveis |
| `page` | integer | Página atual |
| `limit` | integer | Limite de itens por página |
| `offset` | integer | Offset aplicado |
| `has_more` | boolean | Indica se há mais páginas |

### Campos de cada Transferência

| Campo | Tipo | Descrição |
|-------|------|-----------|
| `id` | string | ID único da transferência |
| `resource` | string | Tipo do recurso (sempre "transfer") |
| `amount` | integer | Valor transferido em centavos |
| `original_amount` | integer | Valor original antes de taxas |
| `currency` | string | Moeda (BRL) |
| `status` | string | Status da transferência |
| `description` | string | Descrição da transferência |
| `recipient` | string | ID do vendedor destinatário |
| `sender` | string | ID do marketplace remetente |
| `bank_account` | object | Dados da conta bancária |
| `transfer_date` | string | Data da transferência (YYYY-MM-DD) |
| `transfer_number` | string | Número identificador da transferência |
| `type` | string | Tipo de transferência (automatic, manual) |
| `confirmed` | boolean | Se a transferência foi confirmada |
| `created_at` | string | Data de criação (ISO 8601) |
| `updated_at` | string | Data de atualização (ISO 8601) |

## Códigos de Resposta

| Código | Descrição |
|--------|-----------|
| 200 | Transferências listadas com sucesso |
| 400 | Parâmetros inválidos |
| 401 | Não autenticado ou token inválido |
| 403 | Sem permissão para acessar este vendedor |
| 404 | Vendedor não encontrado |
| 500 | Erro interno do servidor |

## Notas Importantes

* O `seller_id` deve pertencer ao marketplace autenticado
* Valores monetários são sempre em centavos (ex: 50000 = R$ 500,00)
* As datas seguem o padrão ISO 8601
* A paginação é recomendada para listar grandes volumes de transferências
* Transferências podem conter múltiplas transações

## Próximos Passos

* Veja [Detalhes da Transferência](detalhes.md) para obter informações completas
* Consulte [Transações da Transferência](transacoes.md) para ver todas as transações
