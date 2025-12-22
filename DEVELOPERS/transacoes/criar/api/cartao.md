# Criar Transação com Cartão de Crédito/Débito

Crie transações de cartão de crédito ou débito utilizando três métodos diferentes: cartão simples, customer ID ou card ID (tokenizado).

## Endpoint

```
POST /v1/marketplaces/{marketplace_id}/transactions
```

## Modos de Pagamento

### 1. Cartão Simples (Dados Completos)

Use quando o cliente digitar os dados do cartão pela primeira vez.

**⚠️ Segurança**: Nunca armazene `card_number` ou `security_code` em sua aplicação.

### 2. Customer ID

Use quando o cliente já tem um `customer_id` cadastrado com cartões salvos.

### 3. Card ID (Tokenizado)

Use quando você já tokenizou o cartão anteriormente e tem o `card_id`.

---

## Modo 1: Cartão Simples

### Requisição

```http
POST /v1/marketplaces/abc123.../transactions HTTP/1.1
Host: api.gopag.com.br
Authorization: Bearer {access_token}
Content-Type: application/json
```

```json
{
  "payment_type": "credit",
  "on_behalf_of": "54f5445fd4544b454654fdf46545465g",
  "source": {
    "card": {
      "card_number": "4111111111111111",
      "holder_name": "João Silva",
      "expiration_month": "12",
      "expiration_year": "2026",
      "security_code": "123"
    },
    "usage": "single_use",
    "amount": 10000,
    "currency": "BRL",
    "type": "card"
  },
  "description": "Compra de produto X",
  "capture": true,
  "reference_id": "pedido-12345"
}
```

### Parâmetros

| Campo | Tipo | Obrigatório | Descrição |
|-------|------|-------------|-----------|
| `payment_type` | string | Sim | `credit` ou `debit` |
| `on_behalf_of` | string | Sim | ID do vendedor (32 chars hex) |
| `source` | object | Sim | Dados da fonte de pagamento |
| `source.card` | object | Sim | Dados do cartão |
| `source.card.card_number` | string | Sim | Número do cartão (sem espaços) |
| `source.card.holder_name` | string | Sim | Nome como impresso no cartão |
| `source.card.expiration_month` | string | Sim | Mês de expiração (01-12) |
| `source.card.expiration_year` | string | Sim | Ano de expiração (2025, 2026...) |
| `source.card.security_code` | string | Sim | CVV (3-4 dígitos) |
| `source.usage` | string | Não | `single_use` (padrão) ou `reusable` |
| `source.amount` | integer | Não | Valor em centavos (se diferente do total) |
| `source.currency` | string | Não | Moeda (padrão: `BRL`) |
| `source.type` | string | Sim | Sempre `card` |
| `description` | string | Não | Descrição da transação |
| `capture` | boolean | Não | Capturar imediatamente (padrão: `true`) |
| `reference_id` | string | Não | Seu identificador único |

### Campos Opcionais Adicionais

#### Parcelamento (apenas `credit`)

```json
{
  "payment_type": "credit",
  "installment_plan": {
    "number_installments": 3
  }
}
```

| Campo | Tipo | Descrição |
|-------|------|-----------|
| `installment_plan.number_installments` | integer | Número de parcelas (1-12) |

#### 3D Secure

Para transações com autenticação adicional:

```json
{
  "three_d_secure": {
    "on_failure": "continue",
    "device": {
      "java_enabled": false,
      "color_depth": 24,
      "type": "BROWSER",
      "language": "pt-BR",
      "screen_height": 1080,
      "screen_width": 1920,
      "time_zone_offset": -3
    },
    "billing": {
      "address": "Rua Exemplo, 123",
      "city": "São Paulo",
      "postal_code": "01234-567",
      "state": "SP",
      "country": "BR",
      "email_address": "cliente@example.com",
      "phone_number": "(11)98765-4321"
    }
  }
}
```

#### Split de Pagamento

Para dividir o pagamento entre múltiplos recebedores:

```json
{
  "split_rules": [
    {
      "liable": true,
      "charge_processing_fee": false,
      "recipient": "seller-id-1",
      "percentage": 70
    },
    {
      "liable": false,
      "charge_processing_fee": true,
      "recipient": "seller-id-2",
      "percentage": 30
    }
  ]
}
```

### Resposta Sucesso (201 Created)

```json
{
  "resource": "transaction",
  "id": "abc123def456789...",
  "status": "succeeded",
  "amount": 10000,
  "original_amount": 10000,
  "currency": "BRL",
  "description": "Compra de produto X",
  "payment_type": "credit",
  "transaction_number": "123456",
  "gateway_authorizer": "cielo",
  "on_behalf_of": "54f5445fd4544b454654fdf46545465g",
  "confirmed": true,
  "captured": true,
  "refunded": false,
  "voided": false,
  "reference_id": "pedido-12345",
  "payment_method": {
    "id": "card-token-id...",
    "resource": "card",
    "card_brand": "visa",
    "first4_digits": "4111",
    "last4_digits": "1111",
    "expiration_month": "12",
    "expiration_year": "2026",
    "holder_name": "João Silva"
  },
  "payment_authorization": {
    "authorizer_id": "00",
    "authorization_code": "123456",
    "authorization_nsu": "789012"
  },
  "created_at": "2025-12-21T10:30:00Z",
  "updated_at": "2025-12-21T10:30:15Z"
}
```

---

## Modo 2: Customer ID

Use quando o cliente já está cadastrado com cartões salvos.

### Requisição

```json
{
  "payment_type": "credit",
  "on_behalf_of": "54f5445fd4544b454654fdf46545465g",
  "customer": "customer-id-32-chars-hex...",
  "amount": 10000,
  "currency": "BRL",
  "description": "Compra de produto Y",
  "capture": true,
  "reference_id": "pedido-12346",
  "installment_plan": {
    "number_installments": 2
  }
}
```

### Parâmetros

| Campo | Tipo | Obrigatório | Descrição |
|-------|------|-------------|-----------|
| `customer` | string | Sim | ID do comprador cadastrado |
| `amount` | integer | Sim | Valor em centavos |
| `currency` | string | Não | Moeda (padrão: `BRL`) |

**Nota**: O sistema usará o cartão padrão do `customer`.

---

## Modo 3: Card ID (Tokenizado)

Use quando você já tem um cartão tokenizado.

### Requisição

```json
{
  "payment_type": "credit",
  "on_behalf_of": "54f5445fd4544b454654fdf46545465g",
  "source": {
    "card": {
      "id": "card-token-id-32-chars..."
    },
    "currency": "BRL",
    "amount": 10000,
    "type": "card",
    "usage": "single_use"
  },
  "capture": true,
  "reference_id": "pedido-12347"
}
```

### Parâmetros

| Campo | Tipo | Obrigatório | Descrição |
|-------|------|-------------|-----------|
| `source.card.id` | string | Sim | ID do cartão tokenizado |

---

## Cartão de Débito

Para débito, apenas mude o `payment_type`:

```json
{
  "payment_type": "debit",
  "on_behalf_of": "54f5445fd4544b454654fdf46545465g",
  "source": {
    "card": {
      "card_number": "5555555555554444",
      "holder_name": "Maria Santos",
      "expiration_month": "06",
      "expiration_year": "2027",
      "security_code": "456"
    },
    "usage": "single_use",
    "amount": 5000,
    "currency": "BRL",
    "type": "card"
  },
  "description": "Compra à vista",
  "capture": true
}
```

**⚠️ Nota**: Débito não aceita `installment_plan`.

---

## Captura Manual

Para autorizar agora e capturar depois:

### 1. Criar transação sem capturar

```json
{
  "payment_type": "credit",
  "capture": false,
  ...
}
```

**Resposta:**
```json
{
  "status": "pre_authorized",
  "captured": false
}
```

### 2. Capturar posteriormente

```
POST /v1/marketplaces/{marketplace_id}/transactions/{transaction_id}/capture
```

```json
{
  "amount": 10000,
  "on_behalf_of": "54f5445fd4544b454654fdf46545465g"
}
```

Veja mais em [Capturar Transação](../../capturar.md).

---

## Status da Transação

| Status | Descrição |
|--------|-----------|
| `succeeded` | Transação aprovada e capturada |
| `pre_authorized` | Autorizada, aguardando captura |
| `failed` | Transação recusada |
| `pending` | Processando |
| `canceled` | Cancelada |

---

## Erros Comuns

### Campo obrigatório ausente

```json
{
  "status": 400,
  "detail": "Field source.card.card_number is required",
  "trace_id": "a1b2c3"
}
```

### Seller inválido

```json
{
  "status": 401,
  "detail": "Unauthorized to access this seller [d4e5f6]",
  "trace_id": "d4e5f6"
}
```
### Parcelamento inválido

```json
{
  "status": 400,
  "detail": "Field installment_plan.number_installments must be between 1 and 12",
  "trace_id": "j1k2l3"
}
```

---

## Exemplo Completo (cURL)

```bash
curl -X POST https://api.gopag.com.br/v1/marketplaces/abc123.../transactions \  -H "Authorization: Bearer ${ACCESS_TOKEN}" \
  -H "Content-Type: application/json" \
  -d '{
    "payment_type": "credit",
    "on_behalf_of": "54f5445fd4544b454654fdf46545465g",
    "source": {
      "card": {
        "card_number": "4111111111111111",
        "holder_name": "João Silva",
        "expiration_month": "12",
        "expiration_year": "2026",
        "security_code": "123"
      },
      "usage": "single_use",
      "amount": 10000,
      "currency": "BRL",
      "type": "card"
    },
    "installment_plan": {
      "number_installments": 3
    },
    "description": "Compra parcelada",
    "capture": true,
    "reference_id": "pedido-12345"
  }'
```

---

## Próximos Passos

- [Criar Transação PIX](./pix.md)
- [Criar Transação Boleto](./boleto.md)
- [Listar Transações](../../listar.md)
- [Capturar Transação](../../capturar.md)
