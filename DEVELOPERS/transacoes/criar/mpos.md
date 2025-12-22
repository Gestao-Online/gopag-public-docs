# MPOS (Mobile Point of Sale)

Crie transações para maquininhas de cartão (mPOS).

## Endpoint

```
POST /v1/marketplaces/{marketplace_id}/transactions
```

## Request Body

```json
{
  "payment_locale": "mpos",
  "payment_type": "credit",
  "on_behalf_of": "SELLER_ID",
  "description": "Venda produto XYZ",
  "notification": "Pague sua compra no mPOS",
  "installment_plan": {
    "number_installments": 3
  },
  "currency": "BRL",
  "amount": 15000,
  "lock": true,
  "terminal": "a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6",
  "reference_id": "VENDA-12345"
}
```

## Campos

| Campo | Tipo | Obrigatório | Descrição |
|-------|------|-------------|-----------|
| `payment_locale` | string | Sim | Sempre `"mpos"` |
| `payment_type` | string | Sim | `"credit"` ou `"debit"` |
| `on_behalf_of` | string | Sim | ID do vendedor |
| `description` | string | Sim | Descrição da venda |
| `notification` | string | Não | Mensagem exibida no mPOS (máx 200 chars) |
| `installment_plan` | object | Não | Parcelamento (apenas crédito) |
| `currency` | string | Sim | Sempre `"BRL"` |
| `amount` | integer | Sim | Valor em centavos |
| `lock` | boolean | Não | Bloquear mPOS até conclusão (padrão: false) |
| `terminal` | string | Não | Serial number do mPOS específico (32 chars hex) |
| `reference_id` | string | Não | Identificador externo |

## Request

### cURL

```bash
curl --location 'https://api.gopag.com.br/v1/marketplaces/abc123.../transactions' \--header 'Authorization: Bearer SEU_ACCESS_TOKEN' \
--header 'Content-Type: application/json' \
--data '{
  "payment_locale": "mpos",
  "payment_type": "credit",
  "on_behalf_of": "54f5445fd4544b454654fdf46545465g",
  "description": "Venda produto XYZ",
  "notification": "Pague sua compra no mPOS",
  "installment_plan": {
    "number_installments": 3
  },
  "currency": "BRL",
  "amount": 15000,
  "terminal": "a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6"
}'
```

### PHP

```php
<?php
$data = [
    'payment_locale' => 'mpos',
    'payment_type' => 'credit',
    'on_behalf_of' => $sellerId,
    'description' => 'Venda produto XYZ',
    'notification' => 'Pague sua compra no mPOS',
    'installment_plan' => [
        'number_installments' => 3
    ],
    'currency' => 'BRL',
    'amount' => 15000,
    'lock' => true,
    'terminal' => $mposSerialNumber,
    'reference_id' => 'VENDA-12345'
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
    'payment_locale': 'mpos',
    'payment_type': 'credit',
    'on_behalf_of': seller_id,
    'description': 'Venda produto XYZ',
    'notification': 'Pague sua compra no mPOS',
    'installment_plan': {
        'number_installments': 3
    },
    'currency': 'BRL',
    'amount': 15000,
    'lock': True,
    'terminal': mpos_serial,
    'reference_id': 'VENDA-12345'
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
  "id": "abc123def456ghi789...",
  "status": "pending",
  "resource": "transaction",
  "payment_locale": "mpos",
  "payment_type": "credit",
  "amount": "150.00",
  "currency": "BRL",
  "description": "Venda produto XYZ",
  "on_behalf_of": "54f5445fd4544b454654fdf46545465g",
  "installment_plan": {
    "number_installments": 3
  },
  "terminal": "a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6",
  "reference_id": "VENDA-12345",
  "created_at": "2025-12-21T14:30:00Z",
  "updated_at": "2025-12-21T14:30:00Z"
}
```

## Fluxo de Pagamento

```
1. Sistema cria transação via API
   ↓
2. Push notification enviado ao mPOS específico (se terminal informado)
   ou para todos mPOS do vendedor
   ↓
3. Operador vê notificação no mPOS
   "Pague sua compra no mPOS - R$ 150,00"
   ↓
4. Cliente insere/aproxima cartão
   ↓
5. mPOS processa pagamento
   ↓
6. Webhook notifica status (succeeded/failed)
   ↓
7. Sistema libera produto/serviço
```

## Parcelamento

Apenas para `payment_type: credit`:

```json
{
  "installment_plan": {
    "number_installments": 6
  }
}
```

**Limites**:
- Mínimo: 1 parcela
- Máximo: 12 parcelas
- Débito: Sempre 1 parcela

### Exemplo - Parcelamento Variável

```php
<?php
function calcularParcelas($valor) {
    $parcelas = [];
    
    for ($i = 1; $i <= 12; $i++) {
        $valorParcela = $valor / $i;
        
        // Parcela mínima: R$ 5,00
        if ($valorParcela < 500) {
            break;
        }
        
        $parcelas[] = [
            'numero' => $i,
            'valor' => $valorParcela,
            'total' => $valor
        ];
    }
    
    return $parcelas;
}

// Para R$ 150,00
$opcoes = calcularParcelas(15000);
// Retorna: 1x R$150, 2x R$75, 3x R$50, ... até 12x ou mínimo de R$5
?>
```

## Terminal ID

### Formato

- **Tamanho**: 32 caracteres
- **Tipo**: Hexadecimal (0-9, a-f)
- **Exemplo válido**: `a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6`
- **Exemplo inválido**: `ABC-123` ❌

### Obter Terminal ID

```bash
# Listar terminais pareados do vendedor
GET /v1/marketplaces/{id}/sellers/{seller_id}/terminals
```

```json
{
  "items": [
    {
      "id": "a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6",
      "type": "mpos",
      "model": "D195",
      "status": "active",
      "seller_id": "54f5445fd4544b454654fdf46545465g",
      "paired_at": "2025-12-01T10:00:00Z"
    }
  ]
}
```

### Sem Terminal Específico

Se `terminal` não for informado, notificação é enviada para **todos** mPOS do vendedor:

```json
{
  "payment_locale": "mpos",
  "payment_type": "credit",
  "on_behalf_of": "SELLER_ID",
  "amount": 5000
  // terminal omitido
}
```

## Lock Mode

### `lock: true` (Recomendado)

```json
{
  "lock": true
}
```

**Comportamento:**
- ✅ mPOS fica bloqueado para outras transações
- ✅ Operador não consegue iniciar nova venda até conclusão
- ✅ Timeout: 5 minutos
- ✅ Previne múltiplas cobranças acidentais

**Quando usar:**
- Transações de alto valor
- Ambientes com múltiplos operadores
- Necessidade de garantir conclusão

### `lock: false` ou omitido

**Comportamento:**
- ❌ mPOS pode receber novas transações
- ❌ Transação anterior pode ser cancelada
- ✅ Útil para terminais compartilhados em alta rotatividade

## Notificações

Personalize a mensagem exibida no mPOS:

```json
{
  "notification": "Mesa 5 - Conta R$ 150,00"
}
```

**Boas práticas:**
- ✅ Seja claro e objetivo
- ✅ Inclua identificador (mesa, pedido, cliente)
- ✅ Máximo 200 caracteres
- ✅ Use emojis com moderação 🛒
- ❌ Evite informações sensíveis

### Exemplos

```javascript
// Restaurante
{
  notification: "Mesa 12 - Conta R$ 85,50"
}

// Delivery
{
  notification: "Pedido #12345 - Cliente: João Silva"
}

// Loja
{
  notification: "Venda balcão - 3 itens - R$ 299,90"
}

// Feira/Evento
{
  notification: "Stand 8 - Artesanato R$ 45,00"
}
```

## Webhooks

Após processamento no mPOS:

### Sucesso

```json
{
  "event": "transaction.succeeded",
  "transaction_id": "abc123...",
  "payment_locale": "mpos",
  "payment_type": "credit",
  "status": "succeeded",
  "amount": "150.00",
  "terminal": "a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6",
  "card": {
    "brand": "Visa",
    "last4": "1111",
    "entry_mode": "chip"
  },
  "reference_id": "pedido-12345",
  "approved_at": "2025-12-21T14:35:00Z"
}
```

### Falha

```json
{
  "event": "transaction.failed",
  "transaction_id": "abc123...",
  "payment_locale": "mpos",
  "status": "failed",
  "error_code": "card_declined",
  "error_message": "Cartão recusado",
  "reference_id": "pedido-12345",
  "failed_at": "2025-12-21T14:35:00Z"
}
```

## Próximos Passos

- [Tap to Pay](./tap-to-pay.md)
- [PINPAD](./pinpad.md)
- [Link de Pagamento](./link-pagamento.md)
- [Webhooks](../../webhooks/introducao.md)
