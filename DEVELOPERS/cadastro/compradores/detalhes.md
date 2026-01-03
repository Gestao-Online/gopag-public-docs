# Detalhes do Comprador

Endpoints para recuperar e atualizar informações de um comprador.

## Índice

- [Recuperar Detalhes](#recuperar-detalhes)
- [Alterar Detalhes](#alterar-detalhes)

---

## Recuperar Detalhes

### Endpoint

```
GET /v1/marketplaces/{marketplace_id}/buyers/{buyer_id}
```

### Request

```bash
curl --location 'https://api.gopag.com.br/v1/marketplaces/abc123.../buyers/e4e8c5b569da48b28d896385f5481bcf' \
--header 'Authorization: Bearer SEU_ACCESS_TOKEN'
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
    "line3": "",
    "neighborhood": "Jardins",
    "city": "São Paulo",
    "state": "SP",
    "postal_code": "01418000",
    "country_code": "BR"
  },
  "metadata": {},
  "created_at": "2025-12-21T10:00:00Z",
  "updated_at": "2025-12-21T10:00:00Z"
}
```

### Response: 404 Not Found

```json
{
  "status": 404,
  "detail": "Buyer not found",
  "trace_id": "a1b2c3"
}
```

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
curl --location --request PATCH 'https://api.gopag.com.br/v1/marketplaces/abc123.../buyers/e4e8c5b569da48b28d896385f5481bcf' \
--header 'Authorization: Bearer SEU_ACCESS_TOKEN' \
--header 'Content-Type: application/json' \
--data '{
  "email": "maria.novo@email.com",
  "phone_number": "11912345678"
}'
```

### Response: 200 OK

Retorna objeto do comprador com dados atualizados.

```json
{
  "id": "e4e8c5b569da48b28d896385f5481bcf",
  "resource": "buyer",
  "first_name": "Maria",
  "last_name": "Santos",
  "email": "maria.novo@email.com",
  "phone_number": "11912345678",
  "taxpayer_id": "12345678901",
  "birthdate": "1990-07-15",
  "address": {
    "line1": "Nova Rua, 123",
    "neighborhood": "Novo Bairro",
    "city": "São Paulo",
    "state": "SP",
    "postal_code": "01234567",
    "country_code": "BR"
  },
  "metadata": {},
  "created_at": "2025-12-21T10:00:00Z",
  "updated_at": "2025-12-21T16:45:00Z"
}
```

---

## Campos Atualizáveis

| Campo | Tipo | Descrição |
|-------|------|-----------|
| `first_name` | string | Primeiro nome |
| `last_name` | string | Sobrenome |
| `email` | string | Email válido |
| `phone_number` | string | Telefone (apenas números) |
| `birthdate` | string | Data de nascimento (YYYY-MM-DD) |
| `address` | object | Endereço completo |
| `metadata` | object | Dados customizados |

**⚠️ Campos NÃO Atualizáveis**:
- `taxpayer_id` (CPF/CNPJ) - **Não pode ser alterado**
- `id` - ID do comprador
- `resource` - Tipo do recurso
- `created_at` - Data de criação

---

## Exemplos de Uso

### Atualizar Email

```javascript
async function updateBuyerEmail(buyerId, newEmail) {
  const response = await fetch(
    `https://api.gopag.com.br/v1/marketplaces/${marketplaceId}/buyers/${buyerId}`,
    {
      method: 'PATCH',
      headers: {
        'Authorization': `Bearer ${accessToken}`,
        'Content-Type': 'application/json'
      },
      body: JSON.stringify({
        email: newEmail
      })
    }
  );

  return await response.json();
}

// Uso
const updated = await updateBuyerEmail('e4e8c5b...', 'novo@email.com');
console.log('Email atualizado:', updated.email);
```

### Atualizar Endereço Completo

```python
def update_buyer_address(buyer_id, new_address):
    url = f'https://api.gopag.com.br/v1/marketplaces/{marketplace_id}/buyers/{buyer_id}'
    headers = {
        'Authorization': f'Bearer {access_token}',
        'Content-Type': 'application/json'
    }
    data = {
        'address': new_address
    }
    
    response = requests.patch(url, json=data, headers=headers)
    return response.json()

# Uso
new_address = {
    'line1': 'Av. Paulista, 1000',
    'line2': 'Conjunto 101',
    'neighborhood': 'Bela Vista',
    'city': 'São Paulo',
    'state': 'SP',
    'postal_code': '01310100',
    'country_code': 'BR'
}

buyer = update_buyer_address('e4e8c5b...', new_address)
```

### Atualizar Múltiplos Campos

```php
<?php
function updateBuyerData($buyerId, $updates) {
    $url = "https://api.gopag.com.br/v1/marketplaces/{$marketplaceId}/buyers/{$buyerId}";
    
    $ch = curl_init($url);
    curl_setopt($ch, CURLOPT_CUSTOMREQUEST, 'PATCH');
    curl_setopt($ch, CURLOPT_POSTFIELDS, json_encode($updates));
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
    curl_setopt($ch, CURLOPT_HTTPHEADER, [
        'Authorization: Bearer ' . $accessToken,
        'Content-Type: application/json'
    ]);
    
    $response = curl_exec($ch);
    curl_close($ch);
    
    return json_decode($response, true);
}

// Uso: Atualizar vários campos ao mesmo tempo
$updates = [
    'email' => 'novo@email.com',
    'phone_number' => '11999887766',
    'metadata' => [
        'customer_level' => 'gold',
        'last_purchase' => '2025-12-21'
    ]
];

$buyer = updateBuyerData('e4e8c5b...', $updates);
?>
```

### Atualizar Metadata

```javascript
// Adicionar ou atualizar campos em metadata
async function updateBuyerMetadata(buyerId, metadataUpdates) {
  // Primeiro, buscar dados atuais
  const currentBuyer = await getBuyerDetails(buyerId);
  
  // Mesclar metadata existente com novos dados
  const updatedMetadata = {
    ...currentBuyer.metadata,
    ...metadataUpdates
  };
  
  // Atualizar comprador
  return await updateBuyer(buyerId, {
    metadata: updatedMetadata
  });
}

// Uso
await updateBuyerMetadata('e4e8c5b...', {
  loyalty_points: 150,
  vip: true,
  last_interaction: '2025-12-21T10:30:00Z'
});
```

---

## Dicas

### ✅ Boas Práticas

1. **Atualizações parciais**: Envie apenas os campos que realmente mudaram
2. **Validação prévia**: Valide email, telefone e endereço antes de enviar
3. **Preserve metadata**: Ao atualizar metadata, mescle com dados existentes
4. **Trate erros**: Implemente tratamento adequado para erros de validação

### ⚠️ Observações

- `taxpayer_id` (CPF/CNPJ) **não pode ser alterado** após criação
- Para alterar endereço parcialmente, envie o objeto completo com as mudanças
- Campos vazios ou null podem remover dados existentes
- `updated_at` é atualizado automaticamente

---

## Erros Comuns

### Comprador Não Encontrado

```json
{
  "status": 404,
  "detail": "Buyer not found",
  "trace_id": "a1b2c3"
}
```

**Solução**: Verifique se o `buyer_id` está correto.

### Email Inválido

```json
{
  "status": 400,
  "detail": "Invalid email format",
  "trace_id": "d4e5f6"
}
```

**Solução**: Valide formato de email antes de enviar.

### Tentativa de Alterar CPF

```json
{
  "status": 400,
  "detail": "Field taxpayer_id cannot be modified",
  "trace_id": "g7h8i9"
}
```

**Solução**: CPF/CNPJ não pode ser alterado. Crie um novo comprador se necessário.

---

## Próximos Passos

- [Criar Novo Comprador](criar.md)
- [Buscar por CPF/CNPJ](buscar-cpf-cnpj.md)
- [Remover Comprador](remover.md)
- [Criar Transação](../../transacoes/criar/api/cartao.md)
