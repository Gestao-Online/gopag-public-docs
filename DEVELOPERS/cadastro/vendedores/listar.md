# Listar Vendedores

Lista todos os vendedores (sellers) vinculados ao seu marketplace.

## Endpoint

```
GET /v1/marketplaces/{marketplace_id}/sellers
```

## Parâmetros de Query

| Parâmetro | Tipo | Obrigatório | Descrição |
|-----------|------|-------------|-----------|
| `limit` | integer | Não | Quantidade de resultados por página (padrão: 100, máximo: 100) |
| `offset` | integer | Não | Número de registros a pular (padrão: 0) |
| `sort` | string | Não | Campo para ordenação (ex: `created_at`, `-created_at` para desc) |
| `status` | string | Não | Filtrar por status: `pending`, `active`, `inactive` |

## Request

### cURL

```bash
curl --location 'https://api.gopag.com.br/v1/marketplaces/abc123.../sellers?limit=10&offset=0' \--header 'Authorization: Bearer SEU_ACCESS_TOKEN'
```

### PHP

```php
<?php
$ch = curl_init();

curl_setopt_array($ch, [
    CURLOPT_URL => 'https://api.gopag.com.br/v1/marketplaces/abc123.../sellers?limit=10',
    CURLOPT_RETURNTRANSFER => true,    CURLOPT_HTTPHEADER => [
        'Authorization: Bearer ' . $accessToken
    ]
]);

$response = curl_exec($ch);
$data = json_decode($response, true);

curl_close($ch);
?>
```

### Node.js

```javascript
const https = require('https');
const fs = require('fs');

const options = {
  hostname: 'api.gopag.com.br',
  path: '/v1/marketplaces/abc123.../sellers?limit=10',
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
  res.on('end', () => console.log(JSON.parse(data)));
});

req.end();
```

### Python

```python
import requests

response = requests.get(
    'https://api.gopag.com.br/v1/marketplaces/abc123.../sellers',
    params={'limit': 10, 'offset': 0},
    headers={'Authorization': f'Bearer {access_token}'}
)

sellers = response.json()
```

## Response

### Status: 200 OK

```json
{
  "resource": "list",
  "uri": "/v1/marketplaces/abc123.../sellers",
  "limit": 10,
  "offset": 0,
  "has_more": false,
  "query_count": 2,
  "total": 2,
  "items": [
    {
      "id": "17d9e827664b47509f12a082b6047e7a",
      "status": "active",
      "resource": "seller",
      "type": "business",
      "account_balance": 0,
      "current_balance": 0,
      "owner": {
        "first_name": "José",
        "last_name": "Da Silva",
        "email": "jose@empresa.com.br",
        "phone_number": "11987654321",
        "taxpayer_id": "12345678901",
        "birthdate": "1985-03-15"
      },
      "business_name": "Empresa XYZ LTDA",
      "business_phone": "1133334444",
      "business_email": "contato@empresa.com.br",
      "business_website": "https://empresa.com.br",
      "ein": "12345678000190",
      "statement_descriptor": "EMPRESA XYZ",
      "business_address": {
        "line1": "Rua das Flores, 123",
        "line2": "Sala 456",
        "neighborhood": "Centro",
        "city": "São Paulo",
        "state": "SP",
        "postal_code": "01310100",
        "country_code": "BR"
      },
      "mcc": "5411",
      "created_at": "2025-12-01T10:00:00Z",
      "updated_at": "2025-12-15T14:30:00Z"
    },
    {
      "id": "28e0f938775c58610e23b193c7158f8b",
      "status": "pending",
      "resource": "seller",
      "type": "individual",
      "owner": {
        "first_name": "Maria",
        "last_name": "Santos",
        "email": "maria@exemplo.com",
        "phone_number": "11912345678",
        "taxpayer_id": "98765432100",
        "birthdate": "1990-07-20"
      },
      "statement_descriptor": "MARIA SANTOS",
      "created_at": "2025-12-20T08:15:00Z",
      "updated_at": "2025-12-20T08:15:00Z"
    }
  ]
}
```

### Campos da Resposta

| Campo | Tipo | Descrição |
|-------|------|-----------|
| `resource` | string | Sempre `"list"` para listagens |
| `uri` | string | URI da requisição |
| `limit` | integer | Quantidade de itens por página |
| `offset` | integer | Offset aplicado |
| `has_more` | boolean | Indica se há mais páginas |
| `query_count` | integer | Quantidade de itens na página atual |
| `total` | integer | Total de vendedores no marketplace |
| `items` | array | Array de objetos seller |

### Campos do Seller

| Campo | Tipo | Descrição |
|-------|------|-----------|
| `id` | string | ID único do vendedor |
| `status` | string | `pending`, `active`, `inactive` |
| `type` | string | `individual` (pessoa física) ou `business` (pessoa jurídica) |
| `owner.taxpayer_id` | string | CPF do responsável |
| `ein` | string | CNPJ (apenas para `type: business`) |
| `business_name` | string | Razão social (apenas para empresas) |
| `statement_descriptor` | string | Nome que aparece na fatura do cliente |
| `mcc` | string | Código de categoria do comerciante |

## Paginação

Para navegar pelos resultados:

```bash
# Primeira página (itens 0-99)
GET /v1/marketplaces/abc123.../sellers?limit=100&offset=0

# Segunda página (itens 100-199)
GET /v1/marketplaces/abc123.../sellers?limit=100&offset=100

# Terceira página (itens 200-299)
GET /v1/marketplaces/abc123.../sellers?limit=100&offset=200
```

### Exemplo de Loop

```php
<?php
$allSellers = [];
$offset = 0;
$limit = 100;

do {
    $response = getSellers($marketplaceId, $limit, $offset);
    $allSellers = array_merge($allSellers, $response['items']);
    $offset += $limit;
} while ($response['has_more']);

echo "Total de vendedores: " . count($allSellers);
?>
```

## Filtros

### Por Status

```bash
# Apenas vendedores ativos
GET /v1/marketplaces/abc123.../sellers?status=active

# Apenas vendedores pendentes
GET /v1/marketplaces/abc123.../sellers?status=pending
```

### Ordenação

```bash
# Mais recentes primeiro
GET /v1/marketplaces/abc123.../sellers?sort=-created_at

# Mais antigos primeiro
GET /v1/marketplaces/abc123.../sellers?sort=created_at

# Ordem alfabética por razão social
GET /v1/marketplaces/abc123.../sellers?sort=business_name
```

## Erros

### 401 Unauthorized

```json
{
  "status": 401,
  "detail": "Invalid or expired access token",
  "trace_id": "a1b2c3"
}
```

**Solução**: Renove o access token via endpoint `/oauth`

### 403 Forbidden

```json
{
  "status": 403,
  "detail": "Certificate does not match marketplace_id",
  "trace_id": "d4e5f6"
}
```

**Solução**: Verifique se o `certSerialNumber` corresponde ao `marketplace_id`

### 429 Too Many Requests

```json
{
  "status": 429,
  "detail": "Rate limit exceeded. Try again in 60 seconds",
  "trace_id": "g7h8i9"
}
```

**Solução**: Respeite rate limits (Caso necessário entre em contato com o time de suporte)

## Próximos Passos

- [Buscar Vendedor por CPF/CNPJ](./buscar-cpf-cnpj.md)
- [Recuperar Detalhes de Vendedor](./detalhes.md)
- [Códigos MCC](./mcc.md)
- [Criar Transação para Vendedor](../../transacoes/criar/api/cartao.md)
