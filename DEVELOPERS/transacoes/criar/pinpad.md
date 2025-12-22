# PINPAD (Terminal Fixo)

Crie transações para terminais de pagamento fixos (PINPAD) conectados via USB, Bluetooth ou Rede.

## Endpoint

```
POST /v1/marketplaces/{marketplace_id}/transactions
```

## Request Body

```json
{
  "payment_locale": "pinpad",
  "payment_type": "credit",
  "on_behalf_of": "SELLER_ID",
  "description": "Venda Loja Matriz",
  "notification": "Digite a senha no terminal",
  "installment_plan": {
    "number_installments": 6
  },
  "currency": "BRL",
  "amount": 45000,
  "terminal": "pinpad_serial_12345ABCDE",
  "reference_id": "VENDA-LOJA-001"
}
```

## Campos

| Campo | Tipo | Obrigatório | Descrição |
|-------|------|-------------|-----------|
| `payment_locale` | string | Sim | Sempre `"pinpad"` |
| `payment_type` | string | Sim | `"credit"` ou `"debit"` |
| `on_behalf_of` | string | Sim | ID do vendedor |
| `description` | string | Sim | Descrição da venda |
| `notification` | string | Não | Mensagem exibida no PINPAD (máx 32 chars) |
| `installment_plan` | object | Não | Parcelamento (apenas crédito) |
| `currency` | string | Sim | Sempre `"BRL"` |
| `amount` | integer | Sim | Valor em centavos |
| `terminal` | string | Sim | Serial number do PINPAD |
| `reference_id` | string | Não | Identificador externo |

## Request

### cURL

```bash
curl --location 'https://api.gopag.com.br/v1/marketplaces/abc123.../transactions' \--header 'Authorization: Bearer SEU_ACCESS_TOKEN' \
--header 'Content-Type: application/json' \
--data '{
  "payment_locale": "pinpad",
  "payment_type": "credit",
  "on_behalf_of": "54f5445fd4544b454654fdf46545465g",
  "description": "Venda Loja Matriz",
  "notification": "Digite a senha",
  "installment_plan": {
    "number_installments": 6
  },
  "currency": "BRL",
  "amount": 45000,
  "terminal": "pinpad_serial_12345ABCDE"
}'
```

### PHP

```php
<?php
$data = [
    'payment_locale' => 'pinpad',
    'payment_type' => 'credit',
    'on_behalf_of' => $sellerId,
    'description' => 'Venda Loja Matriz',
    'notification' => 'Digite a senha',
    'installment_plan' => [
        'number_installments' => 6
    ],
    'currency' => 'BRL',
    'amount' => 45000,
    'terminal' => $pinpadSerial,
    'reference_id' => 'VENDA-LOJA-001'
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
    'payment_locale': 'pinpad',
    'payment_type': 'credit',
    'on_behalf_of': seller_id,
    'description': 'Venda Loja Matriz',
    'notification': 'Digite a senha',
    'installment_plan': {
        'number_installments': 6
    },
    'currency': 'BRL',
    'amount': 45000,
    'terminal': pinpad_serial,
    'reference_id': 'VENDA-LOJA-001'
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
  "id": "pinpad123abc456...",
  "status": "pending",
  "resource": "transaction",
  "payment_locale": "pinpad",
  "payment_type": "credit",
  "amount": "450.00",
  "currency": "BRL",
  "description": "Venda Loja Matriz",
  "on_behalf_of": "54f5445fd4544b454654fdf46545465g",
  "installment_plan": {
    "number_installments": 6
  },
  "terminal": "pinpad_serial_12345ABCDE",
  "reference_id": "VENDA-LOJA-001",
  "created_at": "2025-12-21T16:00:00Z",
  "updated_at": "2025-12-21T16:00:00Z"
}
```

## Fluxo de Pagamento

```
1. Sistema PDV cria transação via API
   ↓
2. Comando enviado ao PINPAD via USB/Bluetooth/Rede
   ↓
3. PINPAD exibe: "R$ 450,00 - Digite a senha"
   ↓
4. Cliente insere/aproxima/passa cartão
   ↓
5. PINPAD solicita senha (se necessário)
   ↓
6. PINPAD processa pagamento
   ↓
7. PINPAD imprime comprovante (se configurado)
   ↓
8. Webhook notifica sistema
   ↓
9. PDV libera venda
```

## Parcelamento

Configuração de parcelas no PINPAD:

```json
{
  "installment_plan": {
    "number_installments": 12,
  }
}
```

### Tabela de Parcelas Dinâmica

```php
<?php
function gerarTabelaParcelas($valor, $maxParcelas = 12) {
    $parcelas = [];
    
    for ($i = 1; $i <= $maxParcelas; $i++) {
        $valorParcela = $valor / $i;
        
        // Parcela mínima: R$ 10,00
        if ($valorParcela < 1000) {
            break;
        }
        
        $parcelas[] = [
            'numero' => $i,
            'valor_parcela' => $valorParcela,
            'valor_total' => $valor,
            'juros' => 0,  // Sem juros
            'tipo' => $i === 1 ? 'à vista' : "{$i}x sem juros"
        ];
    }
    
    return $parcelas;
}

// Exemplo: R$ 450,00
$opcoes = gerarTabelaParcelas(45000);
/*
[
  { numero: 1, valor_parcela: 45000, tipo: 'à vista' },
  { numero: 2, valor_parcela: 22500, tipo: '2x sem juros' },
  { numero: 3, valor_parcela: 15000, tipo: '3x sem juros' },
  ...
  { numero: 12, valor_parcela: 3750, tipo: '12x sem juros' }
]
*/
?>
```

### Exemplo de comprovante

Com os dados recebidos após a transação confirmada


```
╔════════════════════════════╗
║    LOJA MATRIZ LTDA        ║
║  CNPJ: 12.345.678/0001-99  ║
║  Rua Exemplo, 123 - SP     ║
╠════════════════════════════╣
║  Data: 21/12/2025 16:30    ║
║  Transação: 123456         ║
║                            ║
║  Cartão: **** **** **** 1234║
║  Bandeira: Visa            ║
║  Crédito à vista           ║
║                            ║
║  Valor: R$ 450,00          ║
║  Parcelas: 6x R$ 75,00     ║
║                            ║
║  Status: APROVADO          ║
║  NSU: 789012               ║
║  Código Autorização: 345678║
╠════════════════════════════╣
║  [QR CODE]                 ║
║                            ║
║  _________________________  ║
║  Assinatura do Cliente     ║
║                            ║
║  OBRIGADO PELA PREFERENCIA!║
╚════════════════════════════╝
```

## Webhooks

### Sucesso

```json
{
  "event": "transaction.succeeded",
  "transaction_id": "pinpad123abc...",
  "payment_locale": "pinpad",
  "payment_type": "credit",
  "status": "succeeded",
  "amount": "450.00",
  "installment_plan": {
    "number_installments": 6
  },
  "authorization_code": "345678",
  "nsu": "789012",
  "reference_id": "pedido-12345",
  "approved_at": "2025-12-21T16:02:00Z"
}
```

### Falha

```json
{
  "event": "transaction.failed",
  "transaction_id": "pinpad123abc...",
  "payment_locale": "pinpad",
  "status": "failed",
  "error_code": "card_declined",
  "error_message": "Cartão recusado - saldo insuficiente",
  "reference_id": "pedido-12345",
  "failed_at": "2025-12-21T16:02:00Z"
}
```

## Comparação: Payment Locales

| Característica | PINPAD | mPOS | Tap to Pay |
|----------------|--------|------|------------|
| **Tipo** | Fixo | Móvel | Smartphone |
| **Conexão** | USB/Rede/BT | Bluetooth | NFC |
| **Impressora** | ✅ Sim | ✅ Sim | ❌ Não |
| **Bateria** | Rede elétrica | Bateria própria | Celular |
| **Portabilidade** | ⭐ Baixa | ⭐⭐⭐⭐ Alta | ⭐⭐⭐⭐⭐ Máxima |
| **Ideal para** | Lojas físicas | Delivery, feiras | Vendas rápidas |

## Próximos Passos

- [mPOS](./mpos.md)
- [Tap to Pay](./tap-to-pay.md)
- [Cartão via API](./api/cartao.md)
- [Webhooks](../../webhooks/introducao.md)
- [Listar Transações](../listar.md)
