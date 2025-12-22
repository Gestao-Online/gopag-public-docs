# Transações PIX

Crie cobranças via PIX com QR Code dinâmico.

## Endpoint

```
POST /v1/marketplaces/{marketplace_id}/transactions
```

## Request Body

```json
{
  "payment_type": "pix",
  "on_behalf_of": "SELLER_ID",
  "description": "Pagamento de produto XYZ",
  "currency": "BRL",
  "amount": 15000,
  "pix_expiration_date_time": "2025-12-22 23:59:59",
  "reference_id": "PED-12345"
}
```

### Campos

| Campo | Tipo | Obrigatório | Descrição |
|-------|------|-------------|-----------|
| `payment_type` | string | Sim | Sempre `"pix"` |
| `on_behalf_of` | string | Sim | ID do vendedor (seller_id) |
| `description` | string | Sim | Descrição da cobrança |
| `currency` | string | Sim | Sempre `"BRL"` |
| `amount` | integer | Sim | Valor em centavos (ex: 15000 = R$ 150,00) |
| `pix_expiration_date_time` | string | Não | Data/hora de expiração (formato: YYYY-MM-DD HH:MM:SS) |
| `reference_id` | string | Não | Identificador externo |

## Request

### cURL

```bash
curl --location 'https://api.gopag.com.br/v1/marketplaces/abc123.../transactions' \--header 'Authorization: Bearer SEU_ACCESS_TOKEN' \
--header 'Content-Type: application/json' \
--data '{
  "payment_type": "pix",
  "on_behalf_of": "54f5445fd4544b454654fdf46545465g",
  "description": "Pagamento de produto XYZ",
  "currency": "BRL",
  "amount": 15000,
  "pix_expiration_date_time": "2025-12-22 23:59:59",
  "reference_id": "PED-12345"
}'
```

### PHP

```php
<?php
$data = [
    'payment_type' => 'pix',
    'on_behalf_of' => $sellerId,
    'description' => 'Pagamento de produto XYZ',
    'currency' => 'BRL',
    'amount' => 15000, // R$ 150,00
    'pix_expiration_date_time' => '2025-12-22 23:59:59',
    'reference_id' => 'PED-12345'
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

// Exibir QR Code para o cliente
echo "QR Code: " . $transaction['qr_code'];
echo "URL: " . $transaction['qr_code_url'];
?>
```

### Python

```python
import requests
from datetime import datetime, timedelta

# Expiração: 24 horas a partir de agora
expiration = (datetime.now() + timedelta(days=1)).strftime('%Y-%m-%d %H:%M:%S')

data = {
    'payment_type': 'pix',
    'on_behalf_of': seller_id,
    'description': 'Pagamento de produto XYZ',
    'currency': 'BRL',
    'amount': 15000,  # R$ 150,00
    'pix_expiration_date_time': expiration,
    'reference_id': 'PED-12345'
}

response = requests.post(
    f'https://api.gopag.com.br/v1/marketplaces/{marketplace_id}/transactions',
    json=data,
    headers={'Authorization': f'Bearer {access_token}'}
)

transaction = response.json()
print(f"QR Code: {transaction['qr_code']}")
print(f"URL: {transaction['qr_code_url']}")
```

## Response

### Status: 201 Created

```json
{
  "id": "abc123def456ghi789...",
  "status": "pending",
  "resource": "transaction",
  "payment_type": "pix",
  "amount": "150.00",
  "original_amount": "150.00",
  "currency": "BRL",
  "description": "Pagamento de produto XYZ",
  "on_behalf_of": "54f5445fd4544b454654fdf46545465g",
  "reference_id": "PED-12345",
  "qr_code": "00020126580014br.gov.bcb.pix...",
  "qr_code_url": "https://api.gopag.com.br/qr/abc123...",
  "pix_expiration_date_time": "2025-12-22T23:59:59Z",
  "fees": "1.50",
  "created_at": "2025-12-21T14:30:00Z",
  "updated_at": "2025-12-21T14:30:00Z"
}
```

### Campos da Resposta

| Campo | Descrição |
|-------|-----------|
| `id` | ID único da transação |
| `status` | Sempre `"pending"` na criação |
| `qr_code` | String do QR Code PIX (formato Bacen) |
| `qr_code_url` | URL para gerar imagem do QR Code |
| `pix_expiration_date_time` | Data/hora de expiração |

## Fluxo PIX

```
1. Cliente solicita pagamento
   ↓
2. Loja cria transação PIX
   ↓
3. API retorna QR Code
   ↓
4. Cliente escaneia QR Code no app do banco
   ↓
5. Cliente confirma pagamento
   ↓
6. Loja recebe webhook (status: succeeded)
   ↓
7. Loja libera produto/serviço
```

## Exibir QR Code

### Opção 1: Usar URL fornecida

```html
<img src="https://api.gopag.com.br/qr/abc123..." alt="QR Code PIX" />
```

### Opção 2: Gerar localmente (PHP)

```php
<?php
use Endroid\QrCode\QrCode;

$qrCode = new QrCode($transaction['qr_code']);
$qrCode->setSize(300);

header('Content-Type: ' . $qrCode->getContentType());
echo $qrCode->writeString();
?>
```

### Opção 3: Copiar e colar (PIX Copia e Cola)

```html
<p>Ou copie o código abaixo:</p>
<input type="text" value="<?= $transaction['qr_code'] ?>" readonly />
<button onclick="copyToClipboard()">Copiar código PIX</button>
```

## Verificar Status de Pagamento

### Opção 1: Webhook (Recomendado)

Configure webhook para receber notificação automática quando o PIX for pago:

```json
{
  "event": "transaction.succeeded",
  "transaction_id": "abc123...",
  "status": "succeeded",
  "amount": "150.00",
  "paid_at": "2025-12-21T14:35:00Z"
}
```

### Opção 2: Polling

Consulte o status periodicamente:

```php
<?php
function checkPixPayment($transactionId) {
    $transaction = getTransaction($transactionId);
    return $transaction['status'] === 'succeeded';
}

// Verificar a cada 5 segundos (máximo 5 minutos)
$maxAttempts = 60;
$attempts = 0;

while ($attempts < $maxAttempts) {
    if (checkPixPayment($transactionId)) {
        echo "Pagamento confirmado!";
        break;
    }
    
    sleep(5);
    $attempts++;
}
?>
```

## Expiração do QR Code

- **Padrão**: 24 horas se não especificado
- **Mínimo**: 15 minutos
- **Máximo**: 30 dias

### Exemplo com diferentes expirações

```php
<?php
// Expira em 30 minutos
$expiration = date('Y-m-d H:i:s', strtotime('+30 minutes'));

// Expira em 1 hora
$expiration = date('Y-m-d H:i:s', strtotime('+1 hour'));

// Expira à meia-noite
$expiration = date('Y-m-d 23:59:59');
?>
```

## Erros Comuns

### Valor Inválido

```json
{
  "status": 400,
  "detail": "Amount must be greater than 100 (R$ 1,00)",
  "trace_id": "a1b2c3"
}
```

**Solução**: Valor mínimo é R$ 1,00 (100 centavos)

### Seller Inválido

```json
{
  "status": 400,
  "detail": "Invalid seller_id",
  "trace_id": "d4e5f6"
}
```

**Solução**: Verifique se o `on_behalf_of` contém um seller_id válido do seu marketplace

### Expiração Inválida

```json
{
  "status": 400,
  "detail": "pix_expiration_date_time must be in the future",
  "trace_id": "g7h8i9"
}
```

**Solução**: Data de expiração deve ser futura

## Conciliação

Após pagamento confirmado:

```json
{
  "id": "abc123...",
  "status": "succeeded",
  "payment_type": "pix",
  "amount": "150.00",
  "fees": "1.50",
  "net_amount": "148.50",
  "pix_details": {
    "end_to_end_id": "E12345678202512211435...",
    "paid_at": "2025-12-21T14:35:00Z"
  }
}
```

- `fees`: Taxa cobrada pela transação
- `net_amount`: Valor líquido para o vendedor (amount - fees)
- `end_to_end_id`: ID único do PIX no sistema brasileiro

## Próximos Passos

- [Webhooks](../../../webhooks/introducao.md)
- [Listar Transações](../../listar.md)
- [Detalhes da Transação](../../detalhes.md)
- [Boleto](./boleto.md)
- [Cartão de Crédito](./cartao.md)
