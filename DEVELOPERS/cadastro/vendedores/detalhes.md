# Recuperar Detalhes de Vendedor

Recupera informações completas de um vendedor específico pelo ID.

## Endpoint

```
GET /v1/marketplaces/{marketplace_id}/sellers/{seller_id}
```

## Parâmetros de Path

| Parâmetro | Tipo | Descrição |
|-----------|------|-----------|
| `marketplace_id` | string | ID do marketplace |
| `seller_id` | string | ID único do vendedor |

## Request

### cURL

```bash
curl --location 'https://api.gopag.com.br/v1/marketplaces/abc123.../sellers/17d9e827664b47509f12a082b6047e7a' \--header 'Authorization: Bearer SEU_ACCESS_TOKEN'
```

## Response

### Status: 200 OK

```json
{
  "id": "17d9e827664b47509f12a082b6047e7a",
  "status": "active",
  "resource": "seller",
  "type": "business",
  "account_balance": 15000,
  "current_balance": 15000,
  "fiscal_responsibility": "merchant",
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
  "business_facebook": "https://facebook.com/empresaxyz",
  "business_twitter": "https://twitter.com/empresaxyz",
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
    "line3": "",
    "neighborhood": "Jardim Paulista",
    "city": "São Paulo",
    "state": "SP",
    "postal_code": "01419000",
    "country_code": "BR"
  },
  "delinquent": false,
  "default_debit": "card_abc123",
  "default_credit": "card_def456",
  "mcc": "5732",
  "metadata": {
    "internal_id": "VEND-12345",
    "sales_channel": "marketplace"
  },
  "created_at": "2025-12-01T10:00:00Z",
  "updated_at": "2025-12-15T14:30:00Z"
}
```

### Campos Específicos para Empresas (`type: business`)

Os campos iniciados com `business_` são retornados apenas para vendedores do tipo `business` (pessoa jurídica):

- `business_name` - Razão social
- `business_phone` - Telefone comercial
- `business_email` - Email comercial
- `business_website` - Website
- `business_description` - Descrição do negócio
- `business_facebook` - URL do Facebook
- `business_twitter` - URL do Twitter
- `business_address` - Endereço comercial
- `business_opening_date` - Data de abertura da empresa
- `ein` - CNPJ

Para vendedores `type: individual` (pessoa física), esses campos não estarão presentes.

## Próximos Passos

- [Listar Vendedores](./listar.md)
- [Buscar por CPF/CNPJ](./buscar-cpf-cnpj.md)
- [Códigos MCC](./mcc.md)
