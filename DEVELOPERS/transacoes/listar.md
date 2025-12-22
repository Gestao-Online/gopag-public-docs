# Listar Transações

Lista todas as transações de um vendedor específico.

## Endpoint

```
GET /v1/marketplaces/{marketplace_id}/sellers/{seller_id}/transactions
```

## Parâmetros de Query

| Parâmetro | Tipo | Obrigatório | Descrição |
|-----------|------|-------------|-----------|
| `limit` | integer | Não | Quantidade de resultados por página (padrão: 100, máximo: 100) |
| `offset` | integer | Não | Número de registros a pular (padrão: 0) |
| `sort` | string | Não | Campo para ordenação (`created_at`, `-created_at`) |
| `status` | string | Não | Filtrar por status (ver lista abaixo) |
| `payment_type` | string | Não | Filtrar por tipo: `credit`, `debit`, `boleto`, `bolepix`, `pix` |

## Status de Transação

| Status | Descrição |
|--------|-----------|
| `pending` | Aguardando processamento |
| `pre_authorized` | Pré-autorizada (cartão não presente) |
| `succeeded` | Aprovada e capturada |
| `failed` | Falhou |
| `canceled` | Cancelada |
| `refunded` | Estornada |

## Request

### cURL

```bash
curl --location 'https://api.gopag.com.br/v1/marketplaces/abc123.../sellers/seller456.../transactions?limit=10&status=succeeded' \--header 'Authorization: Bearer SEU_ACCESS_TOKEN'
```

### PHP

```php
<?php
$ch = curl_init();

curl_setopt_array($ch, [
    CURLOPT_URL => "https://api.gopag.com.br/v1/marketplaces/{$marketplaceId}/sellers/{$sellerId}/transactions?limit=10&offset=0",
    CURLOPT_RETURNTRANSFER => true,    CURLOPT_HTTPHEADER => [
        "Authorization: Bearer {$accessToken}"
    ]
]);

$response = curl_exec($ch);
$transactions = json_decode($response, true);

curl_close($ch);
?>
```

### Python

```python
import requests

response = requests.get(
    f'https://api.gopag.com.br/v1/marketplaces/{marketplace_id}/sellers/{seller_id}/transactions',
    params={'limit': 10, 'status': 'succeeded'},
    headers={'Authorization': f'Bearer {access_token}'}
)

transactions = response.json()
```

## Response

### Status: 200 OK

```json
{
  "resource": "list",
  "uri": "/v1/marketplaces/abc123.../sellers/seller456.../transactions",
  "limit": 10,
  "offset": 0,
  "has_more": false,
  "query_count": 2,
  "total": 2,
  "items": [
    {
      "id": "0009462369b14ee596aabfd27faa97f7",
      "status": "succeeded",
      "resource": "transaction",
      "amount": "150.00",
      "original_amount": "150.00",
      "currency": "BRL",
      "description": "Compra produto XYZ",
      "payment_type": "credit",
      "transaction_number": "W-12343124",
      "sales_receipt": "00d5ce5086c84c0f824cf660b020881a",
      "on_behalf_of": "seller456...",
      "statement_descriptor": "MINHA LOJA",
      "payment_method": {
        "id": "2f008ac254964e658be566241cc87c1a",
        "resource": "card",
        "card_brand": "Visa",
        "first4_digits": "4111",
        "last4_digits": "1111",
        "expiration_month": "12",
        "expiration_year": "2026",
        "holder_name": "MARIA SANTOS"
      },
      "refunded": false,
      "voided": false,
      "captured": true,
      "fees": "5.25",
      "expected_on": "2025-12-22T00:00:00Z",
      "created_at": "2025-12-21T14:30:00Z",
      "updated_at": "2025-12-21T14:30:15Z"
    },
    {
      "id": "1110573480c25ff607bbcbe38efba08g8",
      "status": "pending",
      "resource": "transaction",
      "amount": "50.00",
      "currency": "BRL",
      "description": "Pagamento PIX",
      "payment_type": "pix",
      "on_behalf_of": "seller456...",
      "qr_code": "00020126...",
      "qr_code_url": "https://...",
      "created_at": "2025-12-21T15:00:00Z",
      "updated_at": "2025-12-21T15:00:00Z"
    }
  ]
}
```

## Paginação

```bash
# Primeira página
GET /v1/marketplaces/{id}/sellers/{id}/transactions?limit=100&offset=0

# Segunda página
GET /v1/marketplaces/{id}/sellers/{id}/transactions?limit=100&offset=100

# Terceira página
GET /v1/marketplaces/{id}/sellers/{id}/transactions?limit=100&offset=200
```

## Filtros

### Por Status

```bash
# Apenas transações aprovadas
GET .../transactions?status=succeeded

# Apenas pendentes
GET .../transactions?status=pending

# Apenas estornadas
GET .../transactions?status=refunded
```

### Por Tipo de Pagamento

```bash
# Apenas cartão de crédito
GET .../transactions?payment_type=credit

# Apenas PIX
GET .../transactions?payment_type=pix

# Apenas boleto
GET .../transactions?payment_type=boleto
```

### Ordenação

```bash
# Mais recentes primeiro (padrão)
GET .../transactions?sort=-created_at

# Mais antigas primeiro
GET .../transactions?sort=created_at
```

## Próximos Passos

- [Detalhes de Transação](./detalhes.md)
- [Capturar Transação](./capturar.md)
- [Tokenizar Cartão](./tokenizar.md)
- [Criar Transação](./criar/api/cartao.md)
