# Buscar Vendedor por CPF/CNPJ

Busca um vendedor específico utilizando CPF (pessoa física) ou CNPJ (pessoa jurídica).

## Endpoint

```
GET /v1/marketplaces/{marketplace_id}/sellers/search
```

## Parâmetros de Query

| Parâmetro | Tipo | Obrigatório | Descrição |
|-----------|------|-------------|-----------|
| `taxpayer_id` | string | Sim (ou `ein`) | CPF do responsável (11 dígitos, apenas números) |
| `ein` | string | Sim (ou `taxpayer_id`) | CNPJ da empresa (14 dígitos, apenas números) |

**⚠️ IMPORTANTE**: Use `taxpayer_id` para buscar por CPF ou `ein` para buscar por CNPJ. Não envie ambos simultaneamente.

## Request

### Buscar por CPF

#### cURL

```bash
curl --location 'https://api.gopag.com.br/v1/marketplaces/abc123.../sellers/search?taxpayer_id=12345678901' \--header 'Authorization: Bearer SEU_ACCESS_TOKEN'
```

#### PHP

```php
<?php
$taxpayerId = '12345678901'; // CPF sem pontos e hífen

$ch = curl_init();
curl_setopt_array($ch, [
    CURLOPT_URL => "https://api.gopag.com.br/v1/marketplaces/{$marketplaceId}/sellers/search?taxpayer_id={$taxpayerId}",
    CURLOPT_RETURNTRANSFER => true,    CURLOPT_HTTPHEADER => [
        "Authorization: Bearer {$accessToken}"
    ]
]);

$response = curl_exec($ch);
$seller = json_decode($response, true);

curl_close($ch);
?>
```

#### Python

```python
import requests

response = requests.get(
    f'https://api.gopag.com.br/v1/marketplaces/{marketplace_id}/sellers/search',
    params={'taxpayer_id': '12345678901'},
    headers={'Authorization': f'Bearer {access_token}'}
)

seller = response.json()
```

### Buscar por CNPJ

#### cURL

```bash
curl --location 'https://api.gopag.com.br/v1/marketplaces/abc123.../sellers/search?ein=12345678000190' \--header 'Authorization: Bearer SEU_ACCESS_TOKEN'
```

#### Node.js

```javascript
const https = require('https');
const fs = require('fs');

const ein = '12345678000190'; // CNPJ sem pontos, barras e hífen

const options = {
  hostname: 'api.gopag.com.br',
  path: `/v1/marketplaces/${marketplaceId}/sellers/search?ein=${ein}`,
  method: 'GET',
  cert: fs.readFileSync('/etc/ssl/gopag/client.crt'),
  key: fs.readFileSync('/etc/ssl/gopag/client.key'),
  headers: {
    'Authorization': `Bearer ${accessToken}`
  }
};

const req = https.request(options, (res) => {
  let data = '';
  res.on('data', (chunk) => data += chunk);
  res.on('end', () => {
    const seller = JSON.parse(data);
    console.log(seller);
  });
});

req.end();
```

## Response

### Status: 200 OK (Vendedor Encontrado)

```json
{
  "id": "17d9e827664b47509f12a082b6047e7a",
  "status": "active",
  "resource": "seller",
  "type": "business",
  "account_balance": 15000,
  "current_balance": 15000,
  "owner": {
    "first_name": "José",
    "last_name": "Da Silva",
    "email": "jose@empresa.com.br",
    "phone_number": "11987654321",
    "taxpayer_id": "12345678901",
    "birthdate": "1985-03-15"
  },
  "description": "Vendedor de produtos eletrônicos",
  "business_name": "Empresa XYZ LTDA",
  "business_phone": "1133334444",
  "business_email": "contato@empresa.com.br",
  "business_website": "https://empresa.com.br",
  "business_description": "Comércio de eletrônicos e acessórios",
  "ein": "12345678000190",
  "statement_descriptor": "EMPRESA XYZ",
  "business_address": {
    "line1": "Rua das Flores, 123",
    "line2": "Sala 456",
    "line3": "",
    "neighborhood": "Centro",
    "city": "São Paulo",
    "state": "SP",
    "postal_code": "01310100",
    "country_code": "BR"
  },
  "business_opening_date": "2010-05-20",
  "owner_address": {
    "line1": "Rua Principal, 789",
    "line2": "Apto 101",
    "neighborhood": "Jardim Paulista",
    "city": "São Paulo",
    "state": "SP",
    "postal_code": "01419000",
    "country_code": "BR"
  },
  "mcc": "5732",
  "metadata": {
    "internal_id": "VEND-12345",
    "sales_channel": "marketplace"
  },
  "created_at": "2025-12-01T10:00:00Z",
  "updated_at": "2025-12-15T14:30:00Z"
}
```

### Status: 404 Not Found (Vendedor Não Encontrado)

```json
{
  "type": "https://httpstatuses.com/404",
  "title": "Not Found",
  "status": 404,
  "detail": "Seller not found with taxpayer_id: 12345678901",
  "trace_id": "a1b2c3"
}
```

## Casos de Uso

### 1. Validar se Vendedor Já Existe

```php
<?php
function sellerExists($cpf) {
    $response = searchSellerByCPF($cpf);
    return $response['status'] === 200;
}

if (sellerExists('12345678901')) {
    echo "Vendedor já cadastrado!";
} else {
    echo "Vendedor não encontrado. Pode cadastrar.";
}
?>
```

### 2. Obter Seller ID para Criar Transação

```javascript
async function getSellerIdByDocument(ein) {
  const response = await fetch(
    `https://api.gopag.com.br/v1/marketplaces/${marketplaceId}/sellers/search?ein=${ein}`,
    {
      headers: {
        'Authorization': `Bearer ${accessToken}`
      },
      cert: clientCert,
      key: clientKey
    }
  );
  
  if (response.ok) {
    const seller = await response.json();
    return seller.id;
  }
  
  return null;
}

// Uso
const sellerId = await getSellerIdByDocument('12345678000190');
if (sellerId) {
  // Criar transação usando sellerId
}
```

### 3. Sincronizar Status com Sistema Interno

```python
import requests

def sync_seller_status(ein, internal_status):
    response = requests.get(
        f'https://api.gopag.com.br/v1/marketplaces/{marketplace_id}/sellers/search',
        params={'ein': ein},
        headers={'Authorization': f'Bearer {access_token}'}
    )
    
    if response.status_code == 200:
        seller = response.json()
        gopag_status = seller['status']
        
        if gopag_status != internal_status:
            print(f"Status divergente! GoPag: {gopag_status}, Interno: {internal_status}")
            return False
    
    return True
```

## Formato de Documentos

### CPF (`taxpayer_id`)

- **Formato**: Apenas números
- **Tamanho**: Exatamente 11 dígitos
- **Exemplo válido**: `12345678901`
- **Exemplo inválido**: `123.456.789-01` ❌

### CNPJ (`ein`)

- **Formato**: Apenas números
- **Tamanho**: Exatamente 14 dígitos
- **Exemplo válido**: `12345678000190`
- **Exemplo inválido**: `12.345.678/0001-90` ❌

## Erros Comuns

### 400 Bad Request - Formato Inválido

```json
{
  "status": 400,
  "detail": "Invalid taxpayer_id format. Expected 11 digits",
  "trace_id": "d4e5f6"
}
```

**Solução**: Remova pontos, hífens e barras. Envie apenas números.

### 400 Bad Request - Múltiplos Parâmetros

```json
{
  "status": 400,
  "detail": "Provide either taxpayer_id or ein, not both",
  "trace_id": "g7h8i9"
}
```

**Solução**: Envie apenas um dos parâmetros por requisição.

### 400 Bad Request - Nenhum Parâmetro

```json
{
  "status": 400,
  "detail": "Either taxpayer_id or ein is required",
  "trace_id": "j1k2l3"
}
```

**Solução**: Inclua pelo menos um dos parâmetros de busca.

## Performance

- **Cache**: Recomendado cachear resultados por 1 hora
- **Rate Limit**: Máximo 100 requisições/minuto
- **Timeout**: 5 segundos recomendado

### Exemplo com Cache (PHP)

```php
<?php
function getCachedSeller($ein) {
    $cacheKey = "seller_{$ein}";
    $cached = apcu_fetch($cacheKey);
    
    if ($cached !== false) {
        return $cached;
    }
    
    $seller = searchSellerByEIN($ein);
    
    if ($seller) {
        apcu_store($cacheKey, $seller, 3600); // Cache por 1 hora
    }
    
    return $seller;
}
?>
```

## Próximos Passos

- [Listar Todos os Vendedores](./listar.md)
- [Recuperar Detalhes Completos](./detalhes.md)
- [Códigos MCC](./mcc.md)
- [Criar Transação para Vendedor](../../transacoes/criar/api/cartao.md)
