# Boleto Bancário

Gere boletos bancários registrados para pagamento.

## Tipos de Boleto

- **Boleto Simples**: Boleto tradicional
- **Bolepix**: Boleto + PIX (permite pagamento por ambos)

---

## Boleto Simples

### Endpoint

```
POST /v1/marketplaces/{marketplace_id}/transactions
```

### Request Body Mínimo

```json
{
  "payment_type": "boleto",
  "on_behalf_of": "SELLER_ID",
  "customer": "BUYER_ID",
  "amount": 30000,
  "currency": "BRL",
  "description": "Pedido #12345",
  "reference_id": "PED-12345"
}
```

### Request Body Completo

```json
{
  "payment_type": "boleto",
  "on_behalf_of": "54f5445fd4544b454654fdf46545465g",
  "customer": "e4e8c5b569da48b28d896385f5481bcf",
  "amount": 30000,
  "currency": "BRL",
  "description": "Pedido #12345",
  "reference_id": "PED-12345",
  "payment_method": {
    "buyer": {
      "first_name": "Maria",
      "last_name": "Santos",
      "email": "maria@email.com",
      "phone_number": "11987654321",
      "taxpayer_id": "12345678901",
      "birthdate": "1990-07-15",
      "address": {
        "line1": "Rua das Flores, 123",
        "line2": "Apto 456",
        "line3": "",
        "neighborhood": "Centro",
        "city": "São Paulo",
        "state": "SP",
        "postal_code": "01310100"
      }
    },
    "billing_instructions": {
      "late_fee": {
        "mode": "FIXED",
        "amount": 500,
        "start_date": "2025-12-26"
      },
      "interest": {
        "mode": "DAILY_AMOUNT",
        "amount": 100,
        "start_date": "2025-12-26"
      },
      "discount": [
        {
          "mode": "FIXED",
          "amount": 1000,
          "limit_date": "2025-12-23"
        }
      ]
    },
    "expiration_date": "2025-12-25",
    "payment_limit_date": "2025-12-31"
  }
}
```

### Campos do payment_method

| Campo | Tipo | Descrição |
|-------|------|-----------|
| `buyer` | object | Dados do comprador (sobrescreve customer se fornecido) |
| `billing_instructions` | object | Instruções de cobrança (multa, juros, desconto) |
| `expiration_date` | string | Data de vencimento (YYYY-MM-DD) |
| `payment_limit_date` | string | Data limite para pagamento |

### Instruções de Cobrança

#### Multa (late_fee)

```json
{
  "mode": "FIXED",        // ou "PERCENTAGE"
  "amount": 500,          // R$ 5,00 (se FIXED) ou 5% (se PERCENTAGE)
  "percentage": 5,        // Usado se mode = PERCENTAGE
  "start_date": "2025-12-26"  // Dia seguinte ao vencimento
}
```

#### Juros (interest)

```json
{
  "mode": "DAILY_AMOUNT",     // ou "DAILY_PERCENTAGE"
  "amount": 100,               // R$ 1,00 por dia (se DAILY_AMOUNT)
  "percentage": 0.5,          // 0,5% ao dia (se DAILY_PERCENTAGE)
  "start_date": "2025-12-26"
}
```

#### Desconto (discount)

```json
[
  {
    "mode": "FIXED",          // ou "PERCENTAGE"
    "amount": 1000,           // R$ 10,00 de desconto
    "limit_date": "2025-12-23"  // Até 2 dias antes do vencimento
  },
  {
    "mode": "PERCENTAGE",
    "percentage": 5,          // 5% de desconto
    "limit_date": "2025-12-24"  // Até 1 dia antes
  }
]
```

## Response

### Status: 201 Created

```json
{
  "id": "abc123def456...",
  "status": "pending",
  "resource": "transaction",
  "payment_type": "boleto",
  "amount": "300.00",
  "original_amount": "300.00",
  "currency": "BRL",
  "description": "Pedido #12345",
  "on_behalf_of": "54f5445fd4544b454654fdf46545465g",
  "customer": "e4e8c5b569da48b28d896385f5481bcf",
  "reference_id": "PED-12345",
  "boleto": {
    "barcode": "34191790010104351004791020150008291070026000",
    "digitable_line": "34191.79001 01043.510047 91020.150008 2 91070026000000",
    "url": "https://api.gopag.com.br/boleto/abc123...",
    "expiration_date": "2025-12-25"
  },
  "fees": "3.90",
  "created_at": "2025-12-21T14:30:00Z",
  "updated_at": "2025-12-21T14:30:00Z"
}
```

### Campos do Boleto

| Campo | Descrição |
|-------|-----------|
| `barcode` | Código de barras (44 dígitos) |
| `digitable_line` | Linha digitável formatada |
| `url` | URL do boleto em PDF |
| `expiration_date` | Data de vencimento |

---

## Bolepix (Boleto + PIX)

Gera boleto que também pode ser pago via PIX.

### Request Body

```json
{
  "payment_type": "bolepix",
  "on_behalf_of": "54f5445fd4544b454654fdf46545465g",
  "customer": "e4e8c5b569da48b28d896385f5481bcf",
  "amount": 30000,
  "currency": "BRL",
  "description": "Pedido #12345",
  "reference_id": "PED-12345",
  "payment_method": {
    "billing_message_list": {
      "message": "Pedido #12345 - Obrigado pela compra!"
    },
    "interest": {
      "mode": "PERCENTAGE",
      "frequency": "DAILY",
      "value": "0.5",
      "start_at": "2025-12-26"
    },
    "late_fee": {
      "mode": "FIXED",
      "value": "500",
      "start_at": "2025-12-26"
    },
    "discount": {
      "mode": "FIXED",
      "value": "1000",
      "limit_at": "2025-12-23"
    },
    "due_at": "2025-12-25",
    "payment_limit_at": "2025-12-31"
  }
}
```

### Response

```json
{
  "id": "abc123def456...",
  "status": "pending",
  "resource": "transaction",
  "payment_type": "bolepix",
  "amount": "300.00",
  "currency": "BRL",
  "description": "Pedido #12345",
  "boleto": {
    "barcode": "34191790010104351004791020150008291070026000",
    "digitable_line": "34191.79001 01043.510047 91020.150008 2 91070026000000",
    "url": "https://api.gopag.com.br/boleto/abc123...",
    "expiration_date": "2025-12-25"
  },
  "pix": {
    "qr_code": "00020126...",
    "qr_code_url": "https://api.gopag.com.br/qr/abc123...",
    "expiration_date_time": "2025-12-25T23:59:59Z"
  },
  "created_at": "2025-12-21T14:30:00Z"
}
```

**Vantagem**: Cliente escolhe se paga via boleto tradicional ou PIX.

### Via Webhook

```json
{
  "event": "transaction.succeeded",
  "transaction_id": "abc123...",
  "payment_type": "boleto",
  "status": "succeeded",
  "amount": "300.00",
  "paid_at": "2025-12-23T10:45:00Z"
}
```

## Próximos Passos

- [PIX](./pix.md)
- [Cartão de Crédito](./cartao.md)
- [Link de Pagamento](../link-pagamento.md)
- [Webhooks](../../../webhooks/introducao.md)
