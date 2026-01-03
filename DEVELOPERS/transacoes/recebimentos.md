# Recebimentos da Transação

Recupera os recebíveis (receivables) associados a uma transação específica. Os recebíveis representam os valores a receber da transação, incluindo informações sobre datas de pagamento, valores líquidos e taxas.

## Endpoint

```
GET /v1/marketplaces/{marketplace_id}/transactions/{transaction_id}/receivables
```

## Parâmetros de Path

| Parâmetro | Tipo | Descrição |
|-----------|------|-----------|
| `marketplace_id` | string | ID do marketplace |
| `transaction_id` | string | ID único da transação |

## Request

### cURL

```bash
curl --location 'https://api.gopag.com.br/v1/marketplaces/abc123.../transactions/0009462369b14ee596aabfd27faa97f7/receivables' \
--header 'Authorization: Bearer SEU_ACCESS_TOKEN'
```

### PHP

```php
<?php
$marketplaceId = 'seu_marketplace_id';
$transactionId = '0009462369b14ee596aabfd27faa97f7';
$accessToken = 'seu_access_token';

$ch = curl_init();
curl_setopt_array($ch, [
    CURLOPT_URL => "https://api.gopag.com.br/v1/marketplaces/{$marketplaceId}/transactions/{$transactionId}/receivables",
    CURLOPT_RETURNTRANSFER => true,
    CURLOPT_HTTPHEADER => [
        "Authorization: Bearer {$accessToken}"
    ]
]);

$response = curl_exec($ch);
$receivables = json_decode($response, true);

curl_close($ch);

// Processar recebíveis
foreach ($receivables['items'] as $receivable) {
    echo "Valor: R$ " . number_format($receivable['gross_amount'] / 100, 2, ',', '.') . "\n";
    echo "Data prevista: " . $receivable['expected_on'] . "\n";
    echo "Status: " . $receivable['status'] . "\n\n";
}
?>
```

### Python

```python
import requests

marketplace_id = 'seu_marketplace_id'
transaction_id = '0009462369b14ee596aabfd27faa97f7'
access_token = 'seu_access_token'

url = f'https://api.gopag.com.br/v1/marketplaces/{marketplace_id}/transactions/{transaction_id}/receivables'

headers = {
    'Authorization': f'Bearer {access_token}'
}

response = requests.get(url, headers=headers)
receivables = response.json()

# Processar recebíveis
for receivable in receivables['items']:
    print(f"Valor: R$ {receivable['gross_amount'] / 100:.2f}")
    print(f"Data prevista: {receivable['expected_on']}")
    print(f"Status: {receivable['status']}\n")
```

## Response

### Sucesso (200 OK)

```json
{
  "resource": "list",
  "uri": "/v1/marketplaces/abc123.../transactions/0009462369b14ee596aabfd27faa97f7/receivables",
  "items": [
    {
      "id": "rec_abc123...",
      "resource": "receivable",
      "status": "pending",
      "amount": "9500",
      "gross_amount": "10000",
      "anticipation_fee": "0",
      "fraud_coverage_fee": "0",
      "installment": "1",
      "transaction": "0009462369b14ee596aabfd27faa97f7",
      "split_rule": null,
      "liable": true,
      "currency": "BRL",
      "description": "Recebível de transação com cartão",
      "payment_date": null,
      "original_payment_date": null,
      "paid_at": null,
      "canceled_at": null,
      "expected_on": "2025-01-15",
      "created_at": "2025-12-28T10:30:00+00:00",
      "updated_at": "2025-12-28T10:30:00+00:00",
      "uri": "/v1/marketplaces/abc123.../receivables/rec_abc123...",
      "metadata": {}
    }
  ],
  "has_more": false,
  "total": 1,
  "limit": 100,
  "offset": 0
}
```

### Campos da Resposta

| Campo | Tipo | Descrição |
|-------|------|-----------|
| `id` | string | ID único do recebível |
| `resource` | string | Tipo do recurso (sempre "receivable") |
| `status` | string | Status do recebível: `pending`, `paid`, `canceled` |
| `amount` | string | Valor líquido a receber (em centavos) |
| `gross_amount` | string | Valor bruto (em centavos) |
| `anticipation_fee` | string | Taxa de antecipação (em centavos) |
| `fraud_coverage_fee` | string | Taxa de cobertura de fraude (em centavos) |
| `installment` | string | Número da parcela (para pagamentos parcelados) |
| `transaction` | string | ID da transação associada |
| `split_rule` | string/null | ID da regra de split, se aplicável |
| `liable` | boolean | Se o recebível é de responsabilidade do seller |
| `currency` | string | Moeda (BRL) |
| `expected_on` | string | Data prevista de pagamento (YYYY-MM-DD) |
| `paid_at` | string/null | Data/hora efetiva do pagamento |
| `created_at` | string | Data/hora de criação |
| `updated_at` | string | Data/hora da última atualização |

### Status dos Recebíveis

| Status | Descrição |
|--------|-----------|
| `pending` | Aguardando pagamento na data prevista |
| `paid` | Recebível já foi pago |
| `canceled` | Recebível foi cancelado (ex: chargeback, estorno) |

### Exemplo com Parcelamento

Para uma transação parcelada em 3x de R$ 100,00, você receberá 3 recebíveis:

```json
{
  "items": [
    {
      "id": "rec_1...",
      "amount": "9500",
      "gross_amount": "10000",
      "installment": "1",
      "expected_on": "2025-01-15"
    },
    {
      "id": "rec_2...",
      "amount": "9500",
      "gross_amount": "10000",
      "installment": "2",
      "expected_on": "2025-02-15"
    },
    {
      "id": "rec_3...",
      "amount": "9500",
      "gross_amount": "10000",
      "installment": "3",
      "expected_on": "2025-03-15"
    }
  ],
  "total": 3
}
```

## Erros

### 400 - Bad Request

```json
{
  "type": "invalid_request_error",
  "message": "Invalid Transaction ID format"
}
```

### 401 - Unauthorized

```json
{
  "type": "authentication_error",
  "message": "Unauthorized"
}
```

### 404 - Not Found

```json
{
  "type": "invalid_request_error",
  "message": "Transaction or receivables not found"
}
```

## Notas Importantes

- Os recebíveis são criados automaticamente quando uma transação é aprovada
- Para transações parceladas, cada parcela gera um recebível separado
- O valor líquido (`amount`) já desconta as taxas da GoPag
- A data `expected_on` é a data prevista para o pagamento, que pode variar conforme o plano contratado
- Após o pagamento efetivo, o campo `paid_at` será preenchido
- Recebíveis podem ser antecipados através de funcionalidade específica (consulte documentação de antecipação)

## Casos de Uso

### Conciliação Financeira

Use este endpoint para:
- Reconciliar valores a receber com seu sistema contábil
- Verificar datas de repasse
- Calcular fluxo de caixa futuro

### Verificação de Split

Se você usa split de pagamento, os recebíveis mostrarão:
- Quais valores são seus (`liable: true`)
- Quais foram direcionados para outros participantes (`liable: false`)

### Monitoramento de Status

Verifique regularmente o status dos recebíveis para:
- Detectar chargebacks (`status: canceled`)
- Confirmar pagamentos realizados (`status: paid`)
- Acompanhar valores pendentes (`status: pending`)
