# Transações da Transferência

Lista todas as transações que compõem uma transferência específica.

## Endpoint

```
GET /v1/marketplaces/{marketplace_id}/transfers/{transfer_id}/transactions
```

## Parâmetros de URL

| Parâmetro | Tipo | Obrigatório | Descrição |
|-----------|------|-------------|-----------|
| `marketplace_id` | string | Sim | ID do marketplace |
| `transfer_id` | string | Sim | ID da transferência |

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
| `sort` | string | Ordenação dos resultados | `time-descending`, `time-ascending`, `amount:asc` |

## Headers

```
Authorization: Bearer {api_token}
Accept: application/json
```

## Exemplo de Requisição

```bash
curl --request GET \
  --url 'https://api.gopag.com.br/v1/marketplaces/abc123.../transfers/transfer456.../transactions?limit=10' \
  --header 'Authorization: Bearer seu_token_aqui' \
  --header 'Accept: application/json'
```

## Exemplo de Resposta (200 OK)

```json
{
  "id": "transfer456...",
  "resource": "transfer",
  "amount": 50000,
  "original_amount": 52500,
  "currency": "BRL",
  "status": "succeeded",
  "description": "Transferência automática - Semana 03/2024",
  "recipient": "seller123...",
  "sender": "marketplace456...",
  "confirmed": true,
  "bank_account": {
    "id": "bank789...",
    "resource": "bank_account",
    "account_number": "12345",
    "routing_number": "0678",
    "bank_code": "001",
    "holder_name": "João Silva Comercio LTDA",
    "type": "checking"
  },
  "transfer_date": "2024-01-15",
  "transfer_number": "TRF-2024-00123",
  "type": "automatic",
  "transactions": [
    {
      "id": "txn001...",
      "resource": "transaction",
      "status": "succeeded",
      "amount": 25000,
      "original_amount": 26250,
      "currency": "BRL",
      "description": "Venda de produto A - Loja Virtual",
      "payment_type": "credit",
      "transaction_number": "TXN-2024-001234",
      "customer": "customer123...",
      "on_behalf_of": "seller123...",
      "statement_descriptor": "GOPAG*LOJA VIRTUAL",
      "confirmed": true,
      "payment_method": {
        "id": "card123...",
        "resource": "card",
        "card_brand": "visa",
        "first4_digits": "4111",
        "last4_digits": "1111",
        "expiration_month": "12",
        "expiration_year": "2025",
        "holder_name": "MARIA SOUZA"
      },
      "installment_plan": {
        "mode": "interest_free",
        "number_installments": 1
      },
      "refunded": false,
      "voided": false,
      "captured": true,
      "fees": 2500,
      "expected_on": "2024-01-15",
      "created_at": "2024-01-10T14:30:00Z",
      "updated_at": "2024-01-10T14:30:15Z",
      "uri": "/v1/marketplaces/abc123.../transactions/txn001..."
    },
    {
      "id": "txn002...",
      "resource": "transaction",
      "status": "succeeded",
      "amount": 25000,
      "original_amount": 26250,
      "currency": "BRL",
      "description": "Venda de produto B - Loja Física",
      "payment_type": "debit",
      "transaction_number": "TXN-2024-001235",
      "customer": "customer456...",
      "on_behalf_of": "seller123...",
      "statement_descriptor": "GOPAG*LOJA FISICA",
      "confirmed": true,
      "payment_method": {
        "id": "card456...",
        "resource": "card",
        "card_brand": "mastercard",
        "first4_digits": "5555",
        "last4_digits": "4444",
        "expiration_month": "06",
        "expiration_year": "2026",
        "holder_name": "JOSE SANTOS"
      },
      "point_of_sale": {
        "entry_mode": "contactless_chip",
        "identification_number": "POS-123"
      },
      "refunded": false,
      "voided": false,
      "captured": true,
      "fees": 2500,
      "expected_on": "2024-01-15",
      "created_at": "2024-01-11T16:45:00Z",
      "updated_at": "2024-01-11T16:45:12Z",
      "uri": "/v1/marketplaces/abc123.../transactions/txn002..."
    }
  ],
  "metadata": {
    "batch_id": "BATCH-2024-W03"
  },
  "created_at": "2024-01-15T10:00:00Z",
  "updated_at": "2024-01-15T10:05:00Z",
  "uri": "/v1/marketplaces/abc123.../transfers/transfer456..."
}
```

## Campos da Resposta

A resposta retorna um objeto de transferência completo com o array `transactions` contendo todas as transações relacionadas.

### Campos da Transferência

| Campo | Tipo | Descrição |
|-------|------|-----------|
| `id` | string | ID da transferência |
| `resource` | string | Tipo do recurso (transfer) |
| `amount` | integer | Valor líquido transferido em centavos |
| `original_amount` | integer | Valor original antes de taxas |
| `transactions` | array | Array de transações que compõem a transferência |
| ... | ... | Demais campos da transferência |

### Campos de cada Transação

| Campo | Tipo | Descrição |
|-------|------|-----------|
| `id` | string | ID único da transação |
| `resource` | string | Tipo do recurso (transaction) |
| `status` | string | Status da transação |
| `amount` | integer | Valor líquido da transação em centavos |
| `original_amount` | integer | Valor original antes de taxas |
| `currency` | string | Moeda (BRL) |
| `description` | string | Descrição da transação |
| `payment_type` | string | Tipo de pagamento (credit, debit, pix, boleto) |
| `transaction_number` | string | Número identificador da transação |
| `customer` | string | ID do comprador |
| `on_behalf_of` | string | ID do vendedor |
| `payment_method` | object | Dados do método de pagamento |
| `installment_plan` | object | Plano de parcelamento (se aplicável) |
| `point_of_sale` | object | Dados do ponto de venda (se aplicável) |
| `refunded` | boolean | Se foi estornada |
| `voided` | boolean | Se foi cancelada |
| `captured` | boolean | Se foi capturada |
| `fees` | integer | Taxas aplicadas em centavos |
| `expected_on` | string | Data esperada de repasse |
| `created_at` | string | Data de criação (ISO 8601) |
| `updated_at` | string | Data de atualização (ISO 8601) |
| `uri` | string | URI da transação |

## Códigos de Resposta

| Código | Descrição |
|--------|-----------|
| 200 | Transações recuperadas com sucesso |
| 400 | Parâmetros inválidos |
| 401 | Não autenticado ou token inválido |
| 403 | Sem permissão para acessar esta transferência |
| 404 | Transferência não encontrada |
| 500 | Erro interno do servidor |

## Notas Importantes

* A transferência deve pertencer a um vendedor associado ao marketplace autenticado
* Todas as transações listadas já foram processadas e incluídas no valor total da transferência
* Valores monetários são sempre em centavos
* Para detalhes completos de uma transação específica, use a rota [Detalhes da Transação](../transacoes/detalhes.md)

## Próximos Passos

* Veja [Detalhes da Transação](../transacoes/detalhes.md) para informações completas de transações
* Consulte [Detalhes da Transferência](detalhes.md) para informações da transferência
* Entenda [Listar Transações](../transacoes/listar.md) para consultas gerais de transações
