# Compradores (Buyers)

Gerenciamento completo de compradores no GoPag API.

## Índice

- [Criar Comprador](#criar-comprador)
- [Buscar por CPF/CNPJ](#buscar-por-cpfcnpj)
- [Recuperar Detalhes](#recuperar-detalhes)
- [Alterar Detalhes](#alterar-detalhes)
- [Remover Comprador](#remover-comprador)

---

## Criar Comprador

### Endpoint

```
POST /v1/marketplaces/{marketplace_id}/buyers
```

### Request Body

```json
{
  "first_name": "Maria",
  "last_name": "Santos",
  "email": "maria.santos@email.com",
  "phone_number": "11987654321",
  "taxpayer_id": "12345678901",
  "birthdate": "1990-07-15",
  "address": {
    "line1": "Rua das Palmeiras, 456",
    "line2": "Apto 789",
    "line3": "",
    "neighborhood": "Jardins",
    "city": "São Paulo",
    "state": "SP",
    "postal_code": "01418000",
    "country_code": "BR"
  },
  "metadata": {
    "customer_id": "CUST-12345",
    "origin": "mobile_app"
  }
}
```

### Response: 201 Created

```json
{
  "id": "e4e8c5b569da48b28d896385f5481bcf",
  "resource": "buyer",
  "first_name": "Maria",
  "last_name": "Santos",
  "email": "maria.santos@email.com",
  "phone_number": "11987654321",
  "taxpayer_id": "12345678901",
  "birthdate": "1990-07-15",
  "address": {
    "line1": "Rua das Palmeiras, 456",
    "line2": "Apto 789",
    "line3": "",
    "neighborhood": "Jardins",
    "city": "São Paulo",
    "state": "SP",
    "postal_code": "01418000",
    "country_code": "BR"
  },
  "metadata": {
    "customer_id": "CUST-12345",
    "origin": "mobile_app"
  },
  "created_at": "2025-12-21T10:00:00Z",
  "updated_at": "2025-12-21T10:00:00Z"
}
```

### Exemplo cURL

```bash
curl --location 'https://api.gopag.com.br/v1/marketplaces/abc123.../buyers' \--header 'Authorization: Bearer SEU_ACCESS_TOKEN' \
--header 'Content-Type: application/json' \
--data '{
  "first_name": "Maria",
  "last_name": "Santos",
  "email": "maria.santos@email.com",
  "phone_number": "11987654321",
  "taxpayer_id": "12345678901",
  "birthdate": "1990-07-15"
}'
```

---

## Buscar por CPF/CNPJ

### Endpoint

```
GET /v1/marketplaces/{marketplace_id}/buyers/search?taxpayer_id={cpf_cnpj}
```

### Request

```bash
curl --location 'https://api.gopag.com.br/v1/marketplaces/abc123.../buyers/search?taxpayer_id=12345678901' \--header 'Authorization: Bearer SEU_ACCESS_TOKEN'
```

### Response: 200 OK

```json
{
  "id": "e4e8c5b569da48b28d896385f5481bcf",
  "resource": "buyer",
  "first_name": "Maria",
  "last_name": "Santos",
  "email": "maria.santos@email.com",
  "phone_number": "11987654321",
  "taxpayer_id": "12345678901",
  "birthdate": "1990-07-15",
  "address": {
    "line1": "Rua das Palmeiras, 456",
    "line2": "Apto 789",
    "neighborhood": "Jardins",
    "city": "São Paulo",
    "state": "SP",
    "postal_code": "01418000",
    "country_code": "BR"
  },
  "created_at": "2025-12-21T10:00:00Z",
  "updated_at": "2025-12-21T10:00:00Z"
}
```

### Response: 404 Not Found

```json
{
  "status": 404,
  "detail": "Buyer not found with taxpayer_id: 12345678901",
  "trace_id": "a1b2c3"
}
```

---

## Recuperar Detalhes

### Endpoint

```
GET /v1/marketplaces/{marketplace_id}/buyers/{buyer_id}
```

### Request

```bash
curl --location 'https://api.gopag.com.br/v1/marketplaces/abc123.../buyers/e4e8c5b569da48b28d896385f5481bcf' \--header 'Authorization: Bearer SEU_ACCESS_TOKEN'
```

### Response: 200 OK

Retorna objeto completo do comprador (mesmo formato da criação).

---

## Alterar Detalhes

### Endpoint

```
PATCH /v1/marketplaces/{marketplace_id}/buyers/{buyer_id}
```

### Request Body

Envie apenas os campos que deseja atualizar:

```json
{
  "email": "maria.novo@email.com",
  "phone_number": "11912345678",
  "address": {
    "line1": "Nova Rua, 123",
    "neighborhood": "Novo Bairro",
    "city": "São Paulo",
    "state": "SP",
    "postal_code": "01234567",
    "country_code": "BR"
  }
}
```

### Request

```bash
curl --location --request PATCH 'https://api.gopag.com.br/v1/marketplaces/abc123.../buyers/e4e8c5b569da48b28d896385f5481bcf' \--header 'Authorization: Bearer SEU_ACCESS_TOKEN' \
--header 'Content-Type: application/json' \
--data '{
  "email": "maria.novo@email.com",
  "phone_number": "11912345678"
}'
```

### Response: 200 OK

Retorna objeto do comprador com dados atualizados.

---

## Remover Comprador

### Endpoint

```
DELETE /v1/marketplaces/{marketplace_id}/buyers/{buyer_id}
```

### Request

```bash
curl --location --request DELETE 'https://api.gopag.com.br/v1/marketplaces/abc123.../buyers/e4e8c5b569da48b28d896385f5481bcf' \--header 'Authorization: Bearer SEU_ACCESS_TOKEN'
```

### Response: 200 OK

```json
{
  "id": "e4e8c5b569da48b28d896385f5481bcf",
  "resource": "buyer",
  "deleted": true,
  "deleted_at": "2025-12-21T15:30:00Z"
}
```

**⚠️ ATENÇÃO**: 
- A remoção é **permanente** e **irreversível**
- Cartões tokenizados vinculados ao comprador também serão removidos
- Transações passadas NÃO são afetadas (dados históricos preservados)

---

## Campos do Comprador

| Campo | Tipo | Obrigatório | Descrição |
|-------|------|-------------|-----------|
| `first_name` | string | Sim | Primeiro nome |
| `last_name` | string | Sim | Sobrenome |
| `email` | string | Sim | Email válido |
| `phone_number` | string | Não | Telefone (apenas números) |
| `taxpayer_id` | string | Sim | CPF (11 dígitos) ou CNPJ (14 dígitos) |
| `birthdate` | string | Não | Data de nascimento (YYYY-MM-DD) |
| `address` | object | Não | Endereço completo |
| `metadata` | object | Não | Dados customizados (chave-valor) |

### Campos do Endereço

| Campo | Tipo | Obrigatório | Descrição |
|-------|------|-------------|-----------|
| `line1` | string | Sim | Logradouro e número |
| `line2` | string | Não | Complemento |
| `line3` | string | Não | Ponto de referência |
| `neighborhood` | string | Sim | Bairro |
| `city` | string | Sim | Cidade |
| `state` | string | Sim | UF (2 letras) |
| `postal_code` | string | Sim | CEP (8 dígitos, apenas números) |
| `country_code` | string | Sim | Código do país (BR) |

---

## Casos de Uso

### 1. Criar Comprador e Tokenizar Cartão

```php
<?php
// 1. Criar comprador
$buyer = createBuyer([
    'first_name' => 'Maria',
    'last_name' => 'Santos',
    'email' => 'maria@email.com',
    'taxpayer_id' => '12345678901'
]);

$buyerId = $buyer['id'];

// 2. Criar transação com cartão (será tokenizado automaticamente)
$transaction = createTransaction([
    'payment_type' => 'credit',
    'customer' => $buyerId,
    'amount' => 5000,
    'currency' => 'BRL',
    'description' => 'Primeira compra',
    'source' => [
        'card' => [
            'card_number' => '4111111111111111',
            'holder_name' => 'MARIA SANTOS',
            'expiration_month' => '12',
            'expiration_year' => '2028',
            'security_code' => '123'
        ],
        'usage' => 'reusable', // Tokeniza para reutilização
        'type' => 'card'
    ]
]);
?>
```

### 2. Compra Recorrente com Comprador Existente

```javascript
// 1. Buscar comprador por CPF
const buyer = await searchBuyerByCPF('12345678901');

if (buyer) {
  // 2. Criar transação usando customer_id
  const transaction = await createTransaction({
    payment_type: 'credit',
    customer: buyer.id,
    amount: 3000,
    currency: 'BRL',
    description: 'Assinatura mensal',
    capture: true
  });
}
```

### 3. Atualizar Email após Mudança

```python
def update_buyer_email(buyer_id, new_email):
    response = requests.patch(
        f'https://api.gopag.com.br/v1/marketplaces/{marketplace_id}/buyers/{buyer_id}',
        json={'email': new_email},
        headers={'Authorization': f'Bearer {access_token}'}
    )
    return response.json()

# Uso
updated_buyer = update_buyer_email('e4e8c5b...', 'novo@email.com')
```

---

## Erros Comuns

### CPF Já Cadastrado

```json
{
  "status": 400,
  "detail": "Buyer with taxpayer_id 12345678901 already exists",
  "trace_id": "a1b2c3"
}
```

**Solução**: Use busca por CPF para obter ID do comprador existente.

### Email Inválido

```json
{
  "status": 400,
  "detail": "Invalid email format",
  "trace_id": "d4e5f6"
}
```

**Solução**: Valide formato de email antes de enviar.

### CPF Inválido

```json
{
  "status": 400,
  "detail": "Invalid taxpayer_id format. Expected 11 digits for CPF",
  "trace_id": "g7h8i9"
}
```

**Solução**: Envie CPF com 11 dígitos (apenas números, sem pontos ou hífen).

---

## Próximos Passos

- [Criar Transação com Comprador](../../transacoes/criar/api/cartao.md)
- [Tokenizar Cartão](../../transacoes/tokenizar.md)
- [Gestão de Vendedores](../vendedores/listar.md)
