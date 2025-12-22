# Capturar Transação

Captura uma transação pré-autorizada de cartão de crédito (card-not-present).

## Quando Usar

Use este endpoint quando você criou uma transação com `capture: false` e agora deseja capturar o valor pré-autorizado.

**Fluxo típico:**
1. Cliente faz pedido → Cria transação com `capture: false`
2. Produto é separado/confirmado → Captura a transação
3. Pagamento é processado → Cliente é cobrado

**⚠️ IMPORTANTE**: 
- Apenas transações com status `pre_authorized` podem ser capturadas
- Prazo para captura: 5 dias corridos após pré-autorização
- Após o prazo, a pré-autorização é cancelada automaticamente

## Endpoint

```
POST /v1/marketplaces/{marketplace_id}/transactions/{transaction_id}/capture
```

## Parâmetros de Path

| Parâmetro | Tipo | Descrição |
|-----------|------|-----------|
| `marketplace_id` | string | ID do marketplace |
| `transaction_id` | string | ID da transação pré-autorizada |

## Request Body (Opcional)

Você pode capturar um valor diferente do pré-autorizado (menor):

```json
{
  "amount": 12000,
  "on_behalf_of": "SELLER_ID"
}
```

| Campo | Tipo | Obrigatório | Descrição |
|-------|------|-------------|-----------|
| `amount` | integer | Não | Valor a capturar em centavos (deve ser ≤ valor pré-autorizado) |
| `on_behalf_of` | string | Sim | ID do vendedor |

## Request

### Captura Total (Valor Completo)

#### cURL

```bash
curl --location --request POST 'https://api.gopag.com.br/v1/marketplaces/abc123.../transactions/0009462369b14ee596aabfd27faa97f7/capture' \--header 'Authorization: Bearer SEU_ACCESS_TOKEN' \
--header 'Content-Type: application/json' \
--data '{
  "on_behalf_of": "1e5ee2e290d040769806c79e6ef94ee1"
}'
```

#### PHP

```php
<?php
$transactionId = '0009462369b14ee596aabfd27faa97f7';

$data = [
    'on_behalf_of' => $sellerId
];

$ch = curl_init();
curl_setopt_array($ch, [
    CURLOPT_URL => "https://api.gopag.com.br/v1/marketplaces/{$marketplaceId}/transactions/{$transactionId}/capture",
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

### Captura Parcial (Valor Menor)

```bash
curl --location --request POST 'https://api.gopag.com.br/v1/marketplaces/abc123.../transactions/0009462369b14ee596aabfd27faa97f7/capture' \--header 'Authorization: Bearer SEU_ACCESS_TOKEN' \
--header 'Content-Type: application/json' \
--data '{
  "amount": 12000,
  "on_behalf_of": "1e5ee2e290d040769806c79e6ef94ee1"
}'
```

#### Python

```python
import requests

# Captura parcial
data = {
    'amount': 12000,  # R$ 120,00 (de R$ 150,00 pré-autorizados)
    'on_behalf_of': seller_id
}

response = requests.post(
    f'https://api.gopag.com.br/v1/marketplaces/{marketplace_id}/transactions/{transaction_id}/capture',
    json=data,
    headers={'Authorization': f'Bearer {access_token}'}
)

transaction = response.json()
```

## Response

### Status: 200 OK - Captura Total

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
  "fee_details": {
    "amount": "5.25",
    "currency": "BRL",
    "type": "transaction_fee",
    "description": "Taxa de transação"
  },
  "uri": "/v1/marketplaces/abc123.../transactions/0009462369b14ee596aabfd27faa97f7",
  "metadata": {},
  "expected_on": "2025-12-22T00:00:00Z",
  "created_at": "2025-12-21T14:30:00Z",
  "updated_at": "2025-12-21T14:35:00Z"
}
```

### Status: 200 OK - Captura Parcial

```json
{
  "id": "0009462369b14ee596aabfd27faa97f7",
  "status": "succeeded",
  "resource": "transaction",
  "amount": "120.00",
  "original_amount": "150.00",
  "currency": "BRL",
  "captured": true,
  "captured_amount": "120.00",
  "pre_authorized_amount": "150.00",
  "released_amount": "30.00",
  "created_at": "2025-12-21T14:30:00Z",
  "updated_at": "2025-12-21T14:35:00Z"
}
```

**Observações sobre captura parcial:**
- `amount`: Valor efetivamente capturado
- `original_amount`: Valor pré-autorizado original
- `released_amount`: Valor liberado de volta ao limite do cartão

## Casos de Uso

### 1. E-commerce - Captura Após Separação

```php
<?php
function processarPedido($pedidoId) {
    // 1. Cliente finaliza compra
    $transaction = createTransaction([
        'payment_type' => 'credit',
        'amount' => 15000,
        'capture' => false, // Apenas pré-autoriza
        // ... outros campos
    ]);
    
    salvarTransactionId($pedidoId, $transaction['id']);
    
    // 2. Produto é separado e confirmado
    separarProduto($pedidoId);
    
    // 3. Captura o pagamento
    $transactionId = getTransactionId($pedidoId);
    $captured = captureTransaction($transactionId, $sellerId);
    
    if ($captured['status'] === 'succeeded') {
        liberarParaEnvio($pedidoId);
    }
}
?>
```

### 2. Marketplace - Captura com Ajuste de Valor

```javascript
async function capturarComDesconto(transactionId, descontoAplicado) {
  const transaction = await getTransactionDetails(transactionId);
  const valorOriginal = parseFloat(transaction.amount) * 100; // Converter para centavos
  const valorFinal = valorOriginal - descontoAplicado;
  
  // Captura apenas o valor com desconto
  const captured = await captureTransaction(transactionId, {
    amount: valorFinal,
    on_behalf_of: transaction.on_behalf_of
  });
  
  return captured;
}

// Uso: Cliente tinha cupom de desconto de R$ 30,00
await capturarComDesconto('abc123...', 3000);
```

### 3. Reserva de Hotel - Captura Diferenciada

```python
def processar_check_out(reserva_id):
    # Pré-autorização: R$ 500,00 (valor estimado)
    # Check-out: R$ 450,00 (valor real após consumo)
    
    reserva = get_reserva(reserva_id)
    transaction_id = reserva['transaction_id']
    valor_final = calcular_valor_final(reserva_id)  # R$ 450,00
    
    # Captura apenas valor consumido
    captured = capture_transaction(
        transaction_id=transaction_id,
        amount=valor_final * 100,  # Converter para centavos
        on_behalf_of=reserva['seller_id']
    )
    
    # Diferença de R$ 50,00 é liberada automaticamente
    return captured
```

## Erros

### 400 Bad Request - Já Capturada

```json
{
  "status": 400,
  "detail": "Transaction already captured",
  "trace_id": "a1b2c3"
}
```

**Solução**: Esta transação já foi capturada anteriormente.

### 400 Bad Request - Status Inválido

```json
{
  "status": 400,
  "detail": "Transaction status must be pre_authorized to capture",
  "trace_id": "d4e5f6"
}
```

**Solução**: Apenas transações com status `pre_authorized` podem ser capturadas.

### 400 Bad Request - Valor Maior

```json
{
  "status": 400,
  "detail": "Capture amount cannot exceed pre-authorized amount",
  "trace_id": "g7h8i9"
}
```

**Solução**: O valor de captura deve ser menor ou igual ao pré-autorizado.

## Boas Práticas

### ✅ Recomendado

- Capture assim que possível após confirmação
- Use captura parcial quando valor final for menor
- Configure webhooks para monitorar expiração
- Cancele explicitamente se não for capturar

### ❌ Evite

- Deixar pré-autorizações sem ação por dias
- Tentar capturar valores maiores que o pré-autorizado
- Capturar sem verificar disponibilidade de estoque
- Ignorar erros de captura

## Próximos Passos

- [Criar Transação com Pré-autorização](./criar/api/cartao.md)
- [Detalhes da Transação](./detalhes.md)
- [Listar Transações](./listar.md)
