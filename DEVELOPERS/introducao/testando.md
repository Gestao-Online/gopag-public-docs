# Testando a API

Este guia mostra como realizar seus primeiros testes com a GoPag API.

## Pré-requisitos

Antes de começar, certifique-se de ter:

- ✅ Bearer Token (obtido via Portal GoPag ou consultor)
- ✅ Certificado mTLS (ICP-Brasil) instalado e configurado
- ✅ Marketplace ID fornecido pela GoPag
- ✅ Acesso ao ambiente de homologação

## Ferramentas Recomendadas

### cURL

Disponível nativamente em Linux/MacOS, ou via Git Bash/WSL no Windows.

### Postman

- Download: https://www.postman.com/downloads/
- Suporta mTLS nativamente
- Facilita organização de coleções

### Insomnia

- Download: https://insomnia.rest/download
- Interface simples e intuitiva
- Suporte completo a certificados

## Teste 1: Listar Vendedores

Teste básico para validar autenticação completa (mTLS + Bearer Token).

### Request

```bash
curl --location 'https://api-stage.gopag.com.br/v1/marketplaces/SEU_MARKETPLACE_ID/sellers?limit=10' \
--cert /etc/ssl/gopag/client.crt \
--key /etc/ssl/gopag/client.key \
--cacert /etc/ssl/gopag/ca.crt \
--header 'Authorization: Bearer SEU_ACCESS_TOKEN'
```

### Response Esperada

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
      "status": "pending",
      "resource": "seller",
      "type": "business",
      "business_name": "Empresa XYZ LTDA",
      "ein": "12345678000190",
      "created_at": "2025-12-15T10:30:00Z"
    }
  ]
}
```

### ✅ Teste bem-sucedido se:
- Status code: `200 OK`
- Campo `items` retorna array (pode estar vazio)
- Estrutura HAL+JSON válida

## Teste 2: Criar Transação PIX

Teste de criação de transação simples.

### Request

```bash
curl --location 'https://api-stage.gopag.com.br/v1/marketplaces/SEU_MARKETPLACE_ID/transactions' \
--cert /etc/ssl/gopag/client.crt \
--key /etc/ssl/gopag/client.key \
--cacert /etc/ssl/gopag/ca.crt \
--header 'Authorization: Bearer SEU_ACCESS_TOKEN' \
--header 'Content-Type: application/json' \
--data '{
  "payment_type": "pix",
  "on_behalf_of": "SELLER_ID",
  "description": "Teste de integração PIX",
  "currency": "BRL",
  "amount": 1000,
  "pix_expiration_date_time": "2025-12-22 23:59:59"
}'
```

### Response Esperada

```json
{
  "id": "abc123def456...",
  "status": "pending",
  "resource": "transaction",
  "payment_type": "pix",
  "amount": "10.00",
  "currency": "BRL",
  "description": "Teste de integração PIX",
  "on_behalf_of": "SELLER_ID",
  "qr_code": "00020126....",
  "qr_code_url": "https://...",
  "created_at": "2025-12-21T14:30:00Z"
}
```

### ✅ Teste bem-sucedido se:
- Status code: `201 Created`
- Campo `qr_code` presente
- Status inicial: `pending`

## Teste 4: Consultar Transação

Verificar status de transação criada.

### Request

```bash
curl --location 'https://api-stage.gopag.com.br/v1/marketplaces/SEU_MARKETPLACE_ID/transactions/TRANSACTION_ID' \
--cert /etc/ssl/gopag/client.crt \
--key /etc/ssl/gopag/client.key \
--cacert /etc/ssl/gopag/ca.crt \
--header 'Authorization: Bearer SEU_ACCESS_TOKEN'
```

### Response Esperada

```json
{
  "id": "abc123def456...",
  "status": "succeeded",
  "resource": "transaction",
  "payment_type": "pix",
  "amount": "10.00",
  "fees": "0.50",
  "created_at": "2025-12-21T14:30:00Z",
  "updated_at": "2025-12-21T14:35:00Z"
}
```

## Configurando Postman

### 1. Importar Certificados

1. Settings → Certificates → Add Certificate
2. Host: `api-stage.gopag.com.br`
3. CRT file: Selecione `client.crt`
4. KEY file: Selecione `client.key`
5. PFX/P12: (deixe vazio se usar CRT+KEY)

### 2. Criar Environment

```json
{
  "base_url": "https://api-stage.gopag.com.br",
  "marketplace_id": "abc123...",
  "bearer_token": "eyJ0eXAiOiJKV1QiLCJhbGc..."
}
```

### 3. Configurar Authorization

Em cada requisição ou na collection:

1. Authorization → Type: **Bearer Token**
2. Token: `{{bearer_token}}`

## Troubleshooting

### SSL Certificate Error

**Erro:**
```
SSL certificate problem: unable to get local issuer certificate
```

**Solução:**
```bash
# Adicione o CA certificate
curl --cacert /etc/ssl/gopag/ca.crt ...
```

### Bearer Token Inválido

**Erro:**
```json
{
  "status": 401,
  "detail": "Invalid or expired access token"
}
```

**Solução:**
- Verifique se o Bearer Token está correto
- Solicite novo token via Portal GoPag ou consultor

### Marketplace ID Incorreto

**Erro:**
```json
{
  "status": 403,
  "detail": "Certificate serial number does not match marketplace_id"
}
```

**Solução:**
- Verifique se `certSerialNumber` do certificado === `marketplace_id` na URL
- Use `openssl x509 -in client.crt -serial -noout` para verificar

### Campos Inesperados

**Erro:**
```json
{
  "status": 400,
  "detail": "Unexpected fields found: ['campo_invalido']"
}
```

**Solução:**
- A API valida campos estritamente
- Remova campos não documentados do payload

## Próximos Passos

Agora que você testou a API com sucesso:

1. [Criar Transações de Cartão](../transacoes/criar/api/cartao.md)
2. [Gerenciar Vendedores](../cadastro/vendedores/listar.md)
3. [Entender Webhooks](../webhooks/introducao.md)
4. [Códigos de Erro](./codigos-erro.md)

## Ambiente de Produção

Quando estiver pronto para produção:

1. ✅ Substitua base URL para `https://api.gopag.com.br`
2. ✅ Use certificado mTLS de **produção**
3. ✅ Use Bearer Token de **produção**
4. ✅ Configure monitoramento e logs
5. ✅ Implemente tratamento de erros robusto
6. ✅ Configure webhooks para notificações

**⚠️ NUNCA use credenciais de produção em homologação!**
