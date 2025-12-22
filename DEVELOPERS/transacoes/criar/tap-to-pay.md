# Tap to Pay (NFC)

Crie transações por aproximação usando smartphones compatíveis com NFC através do aplicativo GoPag.

Visão do usuário no aplicativo (Android/IOS): https://cdn.gestao.plus/lp/assets/img/gopag/tutorial/tap-to-pay-android.html

## Endpoint

```
POST /v1/marketplaces/{marketplace_id}/transactions
```

## Request Body

```json
{
  "payment_locale": "tap_to_pay",
  "payment_type": "credit",
  "on_behalf_of": "SELLER_ID",
  "description": "Venda produto ABC",
  "notification": "Aproxime o cartão do celular",
  "installment_plan": {
    "number_installments": 1
  },
  "currency": "BRL",
  "amount": 8500,
  "terminal": "smartphone_abc123def456",
  "reference_id": "TAP-54321"
}
```

## Campos

| Campo | Tipo | Obrigatório | Descrição |
|-------|------|-------------|-----------|
| `payment_locale` | string | Sim | Sempre `"tap_to_pay"` |
| `payment_type` | string | Sim | `"credit"` ou `"debit"` |
| `on_behalf_of` | string | Sim | ID do vendedor |
| `description` | string | Sim | Descrição da venda |
| `notification` | string | Não | Mensagem no app (máx 200 chars) |
| `installment_plan` | object | Não | Parcelamento (apenas crédito) |
| `currency` | string | Sim | Sempre `"BRL"` |
| `amount` | integer | Sim | Valor em centavos |
| `terminal` | string | Sim | ID do dispositivo NFC |
| `reference_id` | string | Não | Identificador externo |

## Request

### cURL

```bash
curl --location 'https://api.gopag.com.br/v1/marketplaces/abc123.../transactions' \--header 'Authorization: Bearer SEU_ACCESS_TOKEN' \
--header 'Content-Type: application/json' \
--data '{
  "payment_locale": "tap_to_pay",
  "payment_type": "credit",
  "on_behalf_of": "54f5445fd4544b454654fdf46545465g",
  "description": "Venda produto ABC",
  "notification": "Aproxime o cartão do celular",
  "installment_plan": {
    "number_installments": 1
  },
  "currency": "BRL",
  "amount": 8500,
  "terminal": "smartphone_abc123def456"
}'
```

### PHP

```php
<?php
$data = [
    'payment_locale' => 'tap_to_pay',
    'payment_type' => 'credit',
    'on_behalf_of' => $sellerId,
    'description' => 'Venda produto ABC',
    'notification' => 'Aproxime o cartão do celular',
    'installment_plan' => [
        'number_installments' => 1
    ],
    'currency' => 'BRL',
    'amount' => 8500,
    'terminal' => $deviceId,
    'reference_id' => 'TAP-54321'
];

$ch = curl_init();
curl_setopt_array($ch, [
    CURLOPT_URL => "https://api.gopag.com.br/v1/marketplaces/{$marketplaceId}/transactions",
    CURLOPT_RETURNTRANSFER => true,
    CURLOPT_POST => true,
    CURLOPT_POSTFIELDS => json_encode($data),    CURLOPT_HTTPHEADER => [
        "Authorization: Bearer {$accessToken}",
        'Content-Type: application/json'
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

data = {
    'payment_locale': 'tap_to_pay',
    'payment_type': 'credit',
    'on_behalf_of': seller_id,
    'description': 'Venda produto ABC',
    'notification': 'Aproxime o cartão do celular',
    'installment_plan': {
        'number_installments': 1
    },
    'currency': 'BRL',
    'amount': 8500,
    'terminal': device_id,
    'reference_id': 'TAP-54321'
}

response = requests.post(
    f'https://api.gopag.com.br/v1/marketplaces/{marketplace_id}/transactions',
    json=data,
    headers={'Authorization': f'Bearer {access_token}'}
)

transaction = response.json()
```

## Response

### Status: 201 Created

```json
{
  "id": "tap123abc456def...",
  "status": "pending",
  "resource": "transaction",
  "payment_locale": "tap_to_pay",
  "payment_type": "credit",
  "amount": "85.00",
  "currency": "BRL",
  "description": "Venda produto ABC",
  "on_behalf_of": "54f5445fd4544b454654fdf46545465g",
  "installment_plan": {
    "number_installments": 1
  },
  "terminal": "smartphone_abc123def456",
  "reference_id": "TAP-54321",
  "created_at": "2025-12-21T15:00:00Z",
  "updated_at": "2025-12-21T15:00:00Z"
}
```

## Fluxo de Pagamento

```
1. Sistema cria transação via API
   ↓
2. Push notification enviado ao app do vendedor
   ↓
3. App ativa modo NFC
   "Aguardando aproximação do cartão..."
   ↓
4. Cliente aproxima cartão/smartphone
   ↓
5. App captura dados via NFC
   ↓
6. Processamento da transação
   ↓
7. Feedback visual/sonoro (sucesso/falha)
   ↓
8. Webhook notifica sistema
```

## Requisitos Técnicos

### Dispositivos Compatíveis

Faça o download o app GoPag em https://app.gopag.com.br/download

**Android:**
- ✅ Android 9.0+ (API 28+)
- ✅ Chip NFC ativo
- ✅ Google Play Services atualizado

**iOS:**
- ✅ iPhone XS ou superior
- ✅ iOS 18.4+
- ✅ Chip NFC ativo


## Segurança

### Tokenização Automática

Dados do cartão **NUNCA** são armazenados:

```
Cartão aproximado
    ↓
NFC captura dados
    ↓
Criptografia no dispositivo
    ↓
Token gerado pela PSP
    ↓
Token usado na transação
    ↓
Dados originais descartados ✅
```

### EMV Contactless

Todas as transações seguem padrão EMV:

- ✅ Cryptograma único por transação
- ✅ Contador de transações (ATC)
- ✅ Verificação offline de PIN (se necessário)
- ✅ Certificados digitais validados

## Webhooks

### Sucesso

```json
{
  "event": "transaction.succeeded",
  "transaction_id": "tap123abc...",
  "payment_locale": "tap_to_pay",
  "payment_type": "credit",
  "status": "succeeded",
  "amount": "85.00",
  "terminal": "smartphone_abc123def456",
  "reference_id": "pedido-12345",
  "approved_at": "2025-12-21T15:02:00Z"
}
```

### Falha

```json
{
  "event": "transaction.failed",
  "transaction_id": "tap123abc...",
  "payment_locale": "tap_to_pay",
  "status": "failed",
  "error_code": "nfc_read_error",
  "error_message": "Falha ao ler cartão NFC",
  "reference_id": "pedido-12345",
  "failed_at": "2025-12-21T15:02:00Z"
}
```

## Comparação: Tap to Pay vs mPOS

| Característica | Tap to Pay | mPOS |
|----------------|------------|------|
| **Hardware** | Smartphone NFC | Máquina dedicada |
| **Portabilidade** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ |
| **Velocidade** | ⚡ Muito rápida | ⚡ Rápida |
| **Limite por transação** | Variável | Sem limite |
| **Bateria** | Consome bateria do smartphone | Bateria própria |
| **Melhor para** | Delivery, vendas móveis | Comércio estabelecido |

## Próximos Passos

- [mPOS](./mpos.md)
- [PINPAD](./pinpad.md)
- [Cartão (API)](./api/cartao.md)
- [Webhooks](../../webhooks/introducao.md)
