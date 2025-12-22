# Detalhes da Transação

Recupera informações completas de uma transação específica pelo ID.

## Endpoint

```
GET /v1/marketplaces/{marketplace_id}/transactions/{transaction_id}
```

## Parâmetros de Path

| Parâmetro | Tipo | Descrição |
|-----------|------|-----------|
| `marketplace_id` | string | ID do marketplace |
| `transaction_id` | string | ID único da transação |

## Request

### cURL

```bash
curl --location 'https://api.gopag.com.br/v1/marketplaces/abc123.../transactions/0009462369b14ee596aabfd27faa97f7' \--header 'Authorization: Bearer SEU_ACCESS_TOKEN'
```

### PHP

```php
<?php
$transactionId = '0009462369b14ee596aabfd27faa97f7';

$ch = curl_init();
curl_setopt_array($ch, [
    CURLOPT_URL => "https://api.gopag.com.br/v1/marketplaces/{$marketplaceId}/transactions/{$transactionId}",
    CURLOPT_RETURNTRANSFER => true,    CURLOPT_HTTPHEADER => [
        "Authorization: Bearer {$accessToken}"
    ]
]);

$response = curl_exec($ch);
$transaction = json_decode($response, true);

curl_close($ch);
?>
```

### Python

```python
import requests

response = requests.get(
    f'https://api.gopag.com.br/v1/marketplaces/{marketplace_id}/transactions/{transaction_id}',
    headers={'Authorization': f'Bearer {access_token}'}
)

transaction = response.json()
```

## Response

### Status: 200 OK - Transação de Cartão

```json
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
  "on_behalf_of": "1e5ee2e290d040769806c79e6ef94ee1",
  "statement_descriptor": "MINHA LOJA",
  "payment_method": {
    "id": "2f008ac254964e658be566241cc87c1a",
    "resource": "card",
    "description": "Cartão Visa",
    "card_brand": "Visa",
    "first4_digits": "4111",
    "last4_digits": "1111",
    "expiration_month": "12",
    "expiration_year": "2026",
    "holder_name": "MARIA SANTOS",
    "is_active": true,
    "is_valid": true,
    "is_verified": true,
    "customer": "e4e8c5b569da48b28d896385f5481bcf",
    "fingerprint": "9f2843f0c1b08d6018927b4e5e884a869ae49",
    "uri": "/v1/marketplaces/abc123.../cards/2f008ac254964e658be566241cc87c1a",
    "metadata": {},
    "created_at": "2025-12-20T10:00:00Z",
    "updated_at": "2025-12-20T10:00:00Z"
  },
  "point_of_sale": {
    "entry_mode": "chip",
    "identification_number": "4b7d03e6aa01463bb5673f04a7717db9"
  },
  "installment_plan": {
    "number_installments": 3,
    "installment_amount": "50.00"
  },
  "refunded": false,
  "voided": false,
  "captured": true,
  "fees": "5.25",
  "reference_id": "PED-12345",
  "uri": "/v1/marketplaces/abc123.../transactions/0009462369b14ee596aabfd27faa97f7",
  "metadata": {
    "order_id": "ORDER-12345",
    "customer_ip": "203.0.113.42"
  },
  "expected_on": "2025-12-22T00:00:00Z",
  "created_at": "2025-12-21T14:30:00Z",
  "updated_at": "2025-12-21T14:30:15Z"
}
```

### Status: 200 OK - Transação PIX

```json
{
  "id": "abc123def456ghi789jkl012",
  "status": "succeeded",
  "resource": "transaction",
  "amount": "50.00",
  "original_amount": "50.00",
  "currency": "BRL",
  "description": "Pagamento PIX",
  "payment_type": "pix",
  "on_behalf_of": "1e5ee2e290d040769806c79e6ef94ee1",
  "qr_code": "00020126580014br.gov.bcb.pix...",
  "qr_code_url": "https://api.gopag.com.br/qr/abc123...",
  "pix_expiration_date_time": "2025-12-22T23:59:59Z",
  "pix_details": {
    "end_to_end_id": "E12345678202512211435...",
    "paid_at": "2025-12-21T14:35:00Z"
  },
  "fees": "1.50",
  "reference_id": "PIX-67890",
  "created_at": "2025-12-21T14:30:00Z",
  "updated_at": "2025-12-21T14:35:00Z"
}
```

### Status: 200 OK - Transação Boleto

```json
{
  "id": "def456ghi789jkl012mno345",
  "status": "pending",
  "resource": "transaction",
  "amount": "300.00",
  "original_amount": "300.00",
  "currency": "BRL",
  "description": "Pedido #12345",
  "payment_type": "boleto",
  "on_behalf_of": "1e5ee2e290d040769806c79e6ef94ee1",
  "customer": "e4e8c5b569da48b28d896385f5481bcf",
  "fees": "3.90",
  "reference_id": "BOL-11111",
  "created_at": "2025-12-21T10:00:00Z",
  "updated_at": "2025-12-21T10:00:00Z"
}
```

## Campos da Resposta

### Campos Comuns

| Campo | Tipo | Descrição |
|-------|------|-----------|
| `id` | string | ID único da transação |
| `status` | string | Status atual (ver tabela abaixo) |
| `resource` | string | Sempre `"transaction"` |
| `amount` | string | Valor da transação (formato decimal) |
| `original_amount` | string | Valor original antes de descontos |
| `currency` | string | Moeda (sempre `"BRL"`) |
| `description` | string | Descrição da transação |
| `payment_type` | string | Tipo de pagamento |
| `on_behalf_of` | string | ID do vendedor |
| `fees` | string | Taxa cobrada |
| `reference_id` | string | Identificador externo (opcional) |
| `created_at` | string | Data/hora de criação |
| `updated_at` | string | Data/hora da última atualização |

### Status da Transação

| Status | Descrição |
|--------|-----------|
| `pending` | Aguardando processamento |
| `pre_authorized` | Pré-autorizada (cartão não capturado) |
| `succeeded` | Aprovada e capturada |
| `failed` | Falhou/Negada |
| `canceled` | Cancelada |
| `refunded` | Estornada total ou parcialmente |

### Campos Específicos por Tipo

#### Cartão (`payment_type: credit/debit`)

| Campo | Descrição |
|-------|-----------|
| `payment_method` | Objeto com dados do cartão tokenizado |
| `installment_plan` | Plano de parcelamento (se crédito) |
| `point_of_sale` | Dados do terminal (se presencial) |
| `captured` | Indica se foi capturada |
| `refunded` | Indica se foi estornada |
| `voided` | Indica se foi cancelada |

#### PIX (`payment_type: pix`)

| Campo | Descrição |
|-------|-----------|
| `qr_code` | String do QR Code |
| `qr_code_url` | URL da imagem do QR Code |
| `pix_expiration_date_time` | Data/hora de expiração |
| `pix_details` | Detalhes do pagamento (se pago) |

#### Boleto (`payment_type: boleto/bolepix`)

| Campo | Descrição |
|-------|-----------|
| `boleto.barcode` | Código de barras (44 dígitos) |
| `boleto.digitable_line` | Linha digitável formatada |
| `boleto.url` | URL do PDF do boleto |
| `boleto.expiration_date` | Data de vencimento |

## Casos de Uso

### 1. Verificar Status de Pagamento

```php
<?php
function verificarPagamento($transactionId) {
    $transaction = getTransactionDetails($transactionId);
    
    switch ($transaction['status']) {
        case 'succeeded':
            liberarPedido($transaction['reference_id']);
            return 'Pagamento confirmado';
            
        case 'pending':
            return 'Aguardando pagamento';
            
        case 'failed':
            cancelarPedido($transaction['reference_id']);
            return 'Pagamento falhou';
            
        case 'refunded':
            processarEstorno($transaction['reference_id']);
            return 'Pagamento estornado';
            
        default:
            return 'Status desconhecido';
    }
}
?>
```

### 2. Exibir Comprovante

```javascript
async function exibirComprovante(transactionId) {
  const transaction = await getTransactionDetails(transactionId);
  
  const comprovante = {
    id: transaction.id,
    valor: transaction.amount,
    data: transaction.created_at,
    status: transaction.status,
    forma_pagamento: transaction.payment_type
  };
  
  if (transaction.payment_type === 'credit' || transaction.payment_type === 'debit') {
    comprovante.cartao = {
      bandeira: transaction.payment_method.card_brand,
      final: transaction.payment_method.last4_digits
    };
  }
  
  return comprovante;
}
```

## Erros

### 404 Not Found

```json
{
  "status": 404,
  "detail": "Transaction not found",
  "trace_id": "a1b2c3"
}
```

**Solução**: Verifique se o `transaction_id` está correto.

## Próximos Passos

- [Listar Transações](./listar.md)
- [Capturar Transação](./capturar.md)
- [Criar Transação](./criar/api/cartao.md)
- [Tokenizar Cartão](./tokenizar.md)
