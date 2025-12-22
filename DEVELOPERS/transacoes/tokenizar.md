# Tokenizar Cartão

Crie um token (source) reutilizável de cartão de crédito/débito para cobranças recorrentes.

## Quando Usar

Use tokenização quando você precisa:

- ✅ Cobranças recorrentes (assinaturas)
- ✅ Compras futuras sem re-digitar cartão (one-click)
- ✅ Armazenar cartão de forma segura
- ✅ Evitar tráfego de dados sensíveis de cartão

**⚠️ SEGURANÇA**: 
- Dados completos do cartão (PAN, CVV) **NUNCA** são armazenados
- Apenas token criptografado é retornado
- Token pode ser usado apenas pelo seu marketplace

## Endpoint

```
POST /v1/marketplaces/{marketplace_id}/cards/tokens
```

## Request Body

```json
{
  "holder_name": "MARIA SANTOS",
  "expiration_month": "12",
  "expiration_year": "2026",
  "security_code": "123",
  "card_number": "4111111111111111",
  "customer": "e4e8c5b569da48b28d896385f5481bcf"
}
```

### Campos

| Campo | Tipo | Obrigatório | Descrição |
|-------|------|-------------|-----------|
| `holder_name` | string | Sim | Nome impresso no cartão (CAIXA ALTA) |
| `expiration_month` | string | Sim | Mês de validade (01-12) |
| `expiration_year` | string | Sim | Ano de validade (YYYY) |
| `security_code` | string | Sim | CVV (3 ou 4 dígitos) |
| `card_number` | string | Sim | Número do cartão (13-19 dígitos) |
| `customer` | string | Não | ID do comprador (buyer) |

## Request

### cURL

```bash
curl --location 'https://api.gopag.com.br/v1/marketplaces/abc123.../cards/tokens' \--header 'Authorization: Bearer SEU_ACCESS_TOKEN' \
--header 'Content-Type: application/json' \
--data '{
  "holder_name": "MARIA SANTOS",
  "expiration_month": "12",
  "expiration_year": "2026",
  "security_code": "123",
  "card_number": "4111111111111111",
  "customer": "e4e8c5b569da48b28d896385f5481bcf"
}'
```

### PHP

```php
<?php
$cardData = [
    'holder_name' => strtoupper('Maria Santos'),
    'expiration_month' => '12',
    'expiration_year' => '2026',
    'security_code' => '123',
    'card_number' => '4111111111111111',
    'customer' => $customerId
];

$ch = curl_init();
curl_setopt_array($ch, [
    CURLOPT_URL => "https://api.gopag.com.br/v1/marketplaces/{$marketplaceId}/cards/tokens",
    CURLOPT_RETURNTRANSFER => true,
    CURLOPT_POST => true,
    CURLOPT_POSTFIELDS => json_encode($cardData),    CURLOPT_HTTPHEADER => [
        "Authorization: Bearer {$accessToken}",
        'Content-Type: application/json'
    ]
]);

$response = curl_exec($ch);
$cardToken = json_decode($response, true);

curl_close($ch);

// Armazenar apenas o ID do token
$tokenId = $cardToken['id'];
?>
```

### Python

```python
import requests

card_data = {
    'holder_name': 'MARIA SANTOS',
    'expiration_month': '12',
    'expiration_year': '2026',
    'security_code': '123',
    'card_number': '4111111111111111',
    'customer': customer_id
}

response = requests.post(
    f'https://api.gopag.com.br/v1/marketplaces/{marketplace_id}/cards/tokens',
    json=card_data,
    headers={'Authorization': f'Bearer {access_token}'}
)

card_token = response.json()
token_id = card_token['id']
```

### JavaScript

```javascript
const cardData = {
  holder_name: 'MARIA SANTOS',
  expiration_month: '12',
  expiration_year: '2026',
  security_code: '123',
  card_number: '4111111111111111',
  customer: customerId
};

const response = await fetch(
  `https://api.gopag.com.br/v1/marketplaces/${marketplaceId}/cards/tokens`,
  {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${accessToken}`,
      'Content-Type': 'application/json'
    },
    body: JSON.stringify(cardData),
    cert: clientCert,
    key: clientKey
  }
);

const cardToken = await response.json();
const tokenId = cardToken.id;
```

## Response

### Status: 201 Created

```json
{
  "id": "2f008ac254964e658be566241cc87c1a",
  "resource": "card",
  "card_brand": "Visa",
  "first4_digits": "4111",
  "last4_digits": "1111",
  "expiration_month": "12",
  "expiration_year": "2026",
  "holder_name": "MARIA SANTOS",
  "is_active": true,
  "is_valid": true,
  "is_verified": false,
  "customer": "e4e8c5b569da48b28d896385f5481bcf",
  "fingerprint": "9f2843f0c1b08d6018927b4e5e884a869ae49",
  "uri": "/v1/marketplaces/abc123.../cards/2f008ac254964e658be566241cc87c1a",
  "metadata": {},
  "created_at": "2025-12-21T14:30:00Z",
  "updated_at": "2025-12-21T14:30:00Z"
}
```

### Campos da Resposta

| Campo | Tipo | Descrição |
|-------|------|-----------|
| `id` | string | Token do cartão (use para transações futuras) |
| `card_brand` | string | Bandeira: `Visa`, `Mastercard`, `Elo`, `Amex`, etc |
| `first4_digits` | string | Primeiros 4 dígitos (BIN) |
| `last4_digits` | string | Últimos 4 dígitos |
| `expiration_month` | string | Mês de validade |
| `expiration_year` | string | Ano de validade |
| `holder_name` | string | Nome do portador |
| `is_active` | boolean | Token está ativo |
| `is_valid` | boolean | Cartão é válido |
| `customer` | string | ID do comprador vinculado |
| `fingerprint` | string | Hash único do cartão |

**⚠️ IMPORTANTE**: Armazene apenas o `id`. Nunca armazene dados completos do cartão.

## Usar Token em Transação

Após tokenizar, use o token para criar transações:

### Opção 1: Via Card ID

```json
{
  "payment_type": "credit",
  "source": {
    "card": {
      "id": "2f008ac254964e658be566241cc87c1a"
    },
    "currency": "BRL",
    "amount": 5000,
    "type": "card",
    "usage": "single_use"
  },
  "on_behalf_of": "SELLER_ID",
  "capture": true,
  "reference_id": "RECORRENCIA-001"
}
```

### Opção 2: Via Customer ID

```json
{
  "payment_type": "credit",
  "customer": "e4e8c5b569da48b28d896385f5481bcf",
  "amount": 5000,
  "currency": "BRL",
  "description": "Assinatura mensal",
  "on_behalf_of": "SELLER_ID",
  "capture": true
}
```

## Casos de Uso

### 1. Assinatura Mensal

```php
<?php
// PRIMEIRA COBRANÇA: Tokeniza o cartão
function criarAssinatura($customerId, $cardData) {
    // 1. Tokenizar cartão
    $cardToken = tokenizeCard($cardData, $customerId);
    $cardId = $cardToken['id'];
    
    // 2. Salvar token no banco
    saveCardToken($customerId, $cardId, [
        'brand' => $cardToken['card_brand'],
        'last4' => $cardToken['last4_digits']
    ]);
    
    // 3. Primeira cobrança
    $transaction = createTransaction([
        'payment_type' => 'credit',
        'source' => [
            'card' => ['id' => $cardId],
            'amount' => 9900,
            'currency' => 'BRL',
            'type' => 'card',
            'usage' => 'single_use'
        ],
        'on_behalf_of' => $sellerId,
        'capture' => true
    ]);
    
    return $transaction;
}

// COBRANÇAS RECORRENTES: Usa token salvo
function cobrarAssinaturaMensal($customerId) {
    $cardId = getCardToken($customerId);
    
    $transaction = createTransaction([
        'payment_type' => 'credit',
        'source' => [
            'card' => ['id' => $cardId],
            'amount' => 9900,
            'currency' => 'BRL',
            'type' => 'card',
            'usage' => 'single_use'
        ],
        'on_behalf_of' => $sellerId,
        'capture' => true,
        'description' => 'Assinatura mensal - ' . date('Y-m')
    ]);
    
    return $transaction;
}
?>
```

### 2. Compra com 1 Clique

```javascript
// Salvar cartão na primeira compra
async function primeiraCompra(customerId, cardData, purchaseData) {
  // Tokenizar
  const cardToken = await tokenizeCard(cardData, customerId);
  
  // Perguntar se deseja salvar
  const salvar = confirm('Salvar cartão para compras futuras?');
  
  if (salvar) {
    await saveCardTokenInDatabase(customerId, cardToken.id);
  }
  
  // Processar compra
  return await createTransaction({
    payment_type: 'credit',
    source: {
      card: { id: cardToken.id },
      amount: purchaseData.amount,
      currency: 'BRL',
      type: 'card',
      usage: 'single_use'
    },
    on_behalf_of: purchaseData.sellerId,
    capture: true
  });
}

// Compras futuras - 1 clique
async function compraComUmClique(customerId, purchaseData) {
  const savedCards = await getCustomerSavedCards(customerId);
  
  if (savedCards.length === 0) {
    throw new Error('Nenhum cartão salvo');
  }
  
  // Usar primeiro cartão salvo
  const cardId = savedCards[0].token_id;
  
  return await createTransaction({
    payment_type: 'credit',
    source: {
      card: { id: cardId },
      amount: purchaseData.amount,
      currency: 'BRL',
      type: 'card',
      usage: 'single_use'
    },
    on_behalf_of: purchaseData.sellerId,
    capture: true
  });
}
```

## Segurança

### O que NUNCA armazenar

❌ Número completo do cartão
❌ CVV/CVC
❌ Trilha magnética
❌ PIN

### O que PODE armazenar

✅ Token ID (retornado pela API)
✅ Primeiros 4 dígitos (`first4_digits`)
✅ Últimos 4 dígitos (`last4_digits`)
✅ Bandeira (`card_brand`)
✅ Validade (`expiration_month`, `expiration_year`)
✅ Nome do portador (`holder_name`)

## Validações

### Número do Cartão

```javascript
function validarNumeroCartao(numero) {
  // Remove espaços e hífens
  const limpo = numero.replace(/[\s-]/g, '');
  
  // Verifica tamanho (13-19 dígitos)
  if (!/^\d{13,19}$/.test(limpo)) {
    return false;
  }
  
  // Algoritmo de Luhn
  let soma = 0;
  let alternar = false;
  
  for (let i = limpo.length - 1; i >= 0; i--) {
    let digito = parseInt(limpo[i]);
    
    if (alternar) {
      digito *= 2;
      if (digito > 9) digito -= 9;
    }
    
    soma += digito;
    alternar = !alternar;
  }
  
  return soma % 10 === 0;
}
```

### Data de Validade

```php
<?php
function validarValidade($mes, $ano) {
    $mes = intval($mes);
    $ano = intval($ano);
    
    // Validar mês
    if ($mes < 1 || $mes > 12) {
        return false;
    }
    
    // Comparar com data atual
    $hoje = new DateTime();
    $validade = new DateTime("{$ano}-{$mes}-01");
    $validade->modify('last day of this month');
    
    return $validade >= $hoje;
}
?>
```

## Erros

### 400 Bad Request

```json
{
  "status": 400,
  "detail": "Invalid card xxxx",
  "trace_id": "a1b2c3"
}
```

## Próximos Passos

- [Criar Transação com Token](./criar/api/cartao.md)
- [Gerenciar Compradores](../cadastro/compradores/compradores.md)
- [Listar Transações](./listar.md)
- [Webhooks](../webhooks/introducao.md)
