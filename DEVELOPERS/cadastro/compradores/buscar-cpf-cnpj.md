# Buscar Comprador por CPF/CNPJ

Endpoint para buscar um comprador utilizando CPF ou CNPJ.

## Endpoint

```
GET /v1/marketplaces/{marketplace_id}/buyers/search?taxpayer_id={cpf_cnpj}
```

## Parâmetros

| Parâmetro | Tipo | Obrigatório | Descrição |
|-----------|------|-------------|-----------|
| `taxpayer_id` | string | Sim | CPF (11 dígitos) ou CNPJ (14 dígitos) apenas números |

## Request

```bash
curl --location 'https://api.gopag.com.br/v1/marketplaces/abc123.../buyers/search?taxpayer_id=12345678901' \
--header 'Authorization: Bearer SEU_ACCESS_TOKEN'
```

## Response: 200 OK

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

## Response: 404 Not Found

```json
{
  "status": 404,
  "detail": "Buyer not found with taxpayer_id: 12345678901",
  "trace_id": "a1b2c3"
}
```

---

## Exemplo de Uso

### Verificar se Comprador já Existe

```javascript
async function findOrCreateBuyer(taxpayerId, buyerData) {
  try {
    // Tenta buscar comprador existente
    const response = await fetch(
      `https://api.gopag.com.br/v1/marketplaces/${marketplaceId}/buyers/search?taxpayer_id=${taxpayerId}`,
      {
        headers: {
          'Authorization': `Bearer ${accessToken}`
        }
      }
    );

    if (response.ok) {
      return await response.json(); // Comprador encontrado
    }

    // Se não encontrar (404), cria novo comprador
    return await createBuyer(buyerData);
  } catch (error) {
    console.error('Erro ao buscar/criar comprador:', error);
    throw error;
  }
}
```

### Validar CPF Antes de Criar

```python
import requests

def get_buyer_by_cpf(cpf):
    """Busca comprador por CPF"""
    url = f'https://api.gopag.com.br/v1/marketplaces/{marketplace_id}/buyers/search'
    params = {'taxpayer_id': cpf}
    headers = {'Authorization': f'Bearer {access_token}'}
    
    response = requests.get(url, params=params, headers=headers)
    
    if response.status_code == 200:
        return response.json()
    elif response.status_code == 404:
        return None
    else:
        response.raise_for_status()

# Uso
buyer = get_buyer_by_cpf('12345678901')
if buyer:
    print(f'Comprador já existe: {buyer["id"]}')
else:
    print('Comprador não encontrado, pode criar novo')
```

### Reutilizar Comprador em Nova Transação

```php
<?php
function createTransactionForCPF($cpf, $amount) {
    // Buscar comprador por CPF
    $buyer = searchBuyerByCPF($cpf);
    
    if (!$buyer) {
        throw new Exception("Comprador não encontrado. Crie um novo comprador primeiro.");
    }
    
    // Criar transação usando o buyer_id encontrado
    return createTransaction([
        'payment_type' => 'credit',
        'customer' => $buyer['id'],
        'amount' => $amount,
        'currency' => 'BRL',
        'description' => 'Nova compra',
        'capture' => true
    ]);
}
?>
```

---

## Dicas

### ✅ Boas Práticas

1. **Sempre busque antes de criar**: Evite criar compradores duplicados
2. **Armazene o buyer_id**: Guarde o ID retornado para reutilizar em transações futuras
3. **Use apenas números**: Remova pontos, traços e outros caracteres do CPF/CNPJ
4. **Cache do resultado**: Se vai fazer várias transações seguidas, armazene o buyer_id em memória

### ⚠️ Observações

- CPF deve ter **exatamente 11 dígitos**
- CNPJ deve ter **exatamente 14 dígitos**
- Não envie CPF formatado (ex: 123.456.789-01)
- A busca é case-insensitive e ignora espaços

---

## Erros Comuns

### Formato Inválido

```json
{
  "status": 400,
  "detail": "Invalid taxpayer_id format. Expected 11 or 14 digits",
  "trace_id": "x1y2z3"
}
```

**Solução**: Envie apenas números, sem formatação.

### Marketplace Inválido

```json
{
  "status": 404,
  "detail": "Marketplace not found",
  "trace_id": "a1b2c3"
}
```

**Solução**: Verifique se o `marketplace_id` está correto.

---

## Próximos Passos

- [Criar Novo Comprador](criar.md)
- [Recuperar Detalhes do Comprador](detalhes.md)
- [Atualizar Dados do Comprador](detalhes.md#alterar-detalhes)
- [Remover Comprador](remover.md)
