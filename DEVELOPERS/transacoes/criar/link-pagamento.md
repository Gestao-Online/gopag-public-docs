# Link de Pagamento

Crie um link de pagamento com interface pronta para compartilhar com seus clientes via WhatsApp, email, SMS ou redes sociais.

## Endpoint

```
POST /v1/marketplaces/{marketplace_id}/transactions
```

## Visão Geral

O Link de Pagamento gera uma URL única com interface completa para que o cliente finalize o pagamento sem necessidade de integração frontend. Ideal para:

- 🛍️ **E-commerce**: Checkout rápido
- 💬 **WhatsApp**: Vendas por mensagem
- 📧 **Email**: Cobranças personalizadas
- 📱 **SMS**: Links diretos para pagamento
- 🔗 **Redes Sociais**: Compartilhamento fácil

## Como Funciona

```
1. Você cria a transação com payment_locale: "link_payment"
2. API retorna um link de pagamento único
3. Cliente acessa o link
4. Interface pronta para pagamento (cartão, PIX, boleto)
5. Cliente finaliza o pagamento
6. Você recebe webhook de confirmação
```

---

## Requisição

```http
POST /v1/marketplaces/{marketplace_id}/transactions HTTP/1.1
Host: api.gopag.com.br
Authorization: Bearer {access_token}
Content-Type: application/json
```

```json
{
  "payment_locale": "link_payment",
  "payment_type": "credit",
  "on_behalf_of": "54f5445fd4544b454654fdf46545465g",
  "description": "Cobrança produto XYZ",
  "notification": "Você tem um pagamento pendente",
  "amount": 15000,
  "currency": "BRL",
  "installment_plan": {
    "number_installments": 3
  },
  "reference_id": "pedido-12345"
}
```

---

## Parâmetros

### Obrigatórios

| Campo | Tipo | Descrição |
|-------|------|-----------|
| `payment_locale` | string | Deve ser `link_payment` |
| `payment_type` | string | `credit`, `debit` ou `pix` |
| `on_behalf_of` | string | ID do vendedor (32 chars hex) |
| `description` | string | Descrição do pagamento |
| `amount` | integer | Valor em centavos |

### Opcionais

| Campo | Tipo | Descrição |
|-------|------|-----------|
| `currency` | string | Moeda (padrão: `BRL`) |
| `notification` | string | Mensagem exibida para o cliente |
| `installment_plan` | object | Parcelamento (apenas para `credit`) |
| `installment_plan.number_installments` | integer | Número de parcelas (1-12) |
| `reference_id` | string | Seu identificador único |

---

## Tipos de Pagamento Suportados

### 1. Cartão de Crédito

```json
{
  "payment_locale": "link_payment",
  "payment_type": "credit",
  "on_behalf_of": "54f5445fd4544b454654fdf46545465g",
  "description": "Compra parcelada",
  "amount": 30000,
  "installment_plan": {
    "number_installments": 6
  },
  "reference_id": "pedido-001"
}
```

**Interface gerada:**
- ✅ Formulário de cartão de crédito
- ✅ Seletor de parcelas
- ✅ Validação em tempo real
- ✅ 3D Secure automático

### 2. PIX

```json
{
  "payment_locale": "link_payment",
  "payment_type": "pix",
  "on_behalf_of": "54f5445fd4544b454654fdf46545465g",
  "description": "Pagamento via PIX",
  "amount": 10000,
  "reference_id": "pedido-003"
}
```

**Interface gerada:**
- ✅ QR Code PIX
- ✅ Código PIX copia e cola
- ✅ Confirmação automática
- ✅ Timer de expiração

## Parâmetro `notification`

Mensagem personalizada exibida para o usuário

```json
{
  "notification": "Olá João! Complete seu pagamento aqui 😊"
}
```

**Boas práticas:**
- Use o nome do cliente
- Seja claro e objetivo
- Inclua emojis para humanizar
- Máximo 200 caracteres

---

## Resposta de Sucesso

```json
{
  "id": "e7f8g9h0i1j2k3l4m5n6o7p8q9r0s1t2",
  "status": "pending",
  "payment_locale": "link_payment",
  "payment_type": "credit",
  "amount": 15000,
  "currency": "BRL",
  "description": "Cobrança produto XYZ",
  "on_behalf_of": "54f5445fd4544b454654fdf46545465g",
  "payment_link": {
    "url": "https://app.gopag.com.br/u/?uuid=cc378516-4a29-11f0-8331-xxxxxx",
    "expires_at": "2025-12-28T23:59:59Z"
  },
  "created_at": "2025-12-21T14:30:00Z",
  "trace_id": "a1b2c3"
}
```

### Campos da Resposta

- **payment_link.url**: URL do link de pagamento a ser compartilhado
- **payment_link.expires_at**: Data de expiração do link (padrão: 7 dias)
- **status**: Status inicial sempre `pending`

---

## Exemplo Completo

```bash
curl -X POST https://api.gopag.com.br/v1/marketplaces/abc123.../transactions \  -H "Authorization: Bearer ${ACCESS_TOKEN}" \
  -H "Content-Type: application/json" \
  -d '{
    "payment_locale": "link_payment",
    "payment_type": "credit",
    "on_behalf_of": "54f5445fd4544b454654fdf46545465g",
    "description": "Compra de produto XYZ",
    "notification": "Complete seu pagamento em até 24h",
    "amount": 15000,
    "currency": "BRL",
    "installment_plan": {
      "number_installments": 3
    },
    "reference_id": "pedido-12345"
  }'
```

---

## Segurança

### Links Únicos
Cada link é gerado com ID único e não pode ser reutilizado.

### Expiração
Links expiram após 7 dias (configurável).

### HTTPS Obrigatório
Todas as páginas de pagamento usam HTTPS.

### Segurança dos Dados
Dados de cartão são processados diretamente pelo gateway e nunca passam por seus servidores.

---

## Erros Comuns

### Campo obrigatório ausente

```json
{
  "status": 400,
  "detail": "Field description is required",
  "trace_id": "d4e5f6"
}
```

### Seller não autorizado

```json
{
  "status": 401,
  "detail": "Unauthorized to access this seller [j1k2l3]",
  "trace_id": "j1k2l3"
}
```

---

## Recursos do Link de Pagamento

O link de pagamento gerado possui os seguintes recursos:

- ✅ **Link único e seguro**: Cada transação gera um link exclusivo
- ✅ **Interface responsiva**: Funciona em desktop, mobile e tablet
- ✅ **Múltiplos métodos**: Suporte a cartão de crédito, débito e PIX
- ✅ **QR Code**: Compartilhamento rápido via QR Code
- ✅ **Expiração configurável**: Controle de validade do link

---


## Webhooks

Após processamento na interface de link de pagamento:

### Sucesso

```json
{
  "event": "transaction.succeeded",
  "transaction_id": "abc123...",
  "payment_locale": "payment_link",
  "payment_type": "credit",
  "status": "succeeded",
  "amount": "150.00",
  "terminal": "a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6",
  "card": {
    "brand": "Visa",
    "last4": "1111",
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
  "payment_locale": "payment_link",
  "status": "failed",
  "error_code": "card_declined",
  "error_message": "Cartão recusado",
  "reference_id": "pedido-12345",
  "failed_at": "2025-12-21T14:35:00Z"
}
```

## Próximos Passos

- [MPOS](./mpos.md)
- [Tap to Pay](./tap-to-pay.md)
- [PINPAD](./pinpad.md)
- [Transações via API](./api/cartao.md)

