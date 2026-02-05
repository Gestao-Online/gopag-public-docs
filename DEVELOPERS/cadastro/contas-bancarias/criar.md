# Criar Conta Bancária

Processo de tokenização e associação de contas bancárias para vendedores no GoPag API.

## Visão Geral

A criação de uma conta bancária é feita em **dois passos**:

1. **Criar Token** - Tokenizar os dados bancários sensíveis
2. **Associar ao Vendedor** - Vincular o token ao vendedor

Este processo garante que dados sensíveis nunca sejam armazenados diretamente.

---

## Passo 1: Criar Token de Conta Bancária

### Endpoint

```
POST /v1/marketplaces/{marketplace_id}/bank_accounts/tokens
```

### Request Body

```json
{
  "holder_name": "TECH EXEMPLO COMPANY LTDA",
  "ein": "12345678000190",
  "bank_code": "104",
  "type": "checking",
  "account_number": "664",
  "routing_number": "4928"
}
```

### Response: 201 Created

```json
{
  "id": "387fc5b1f107468a8ddc3871cef5f0ec",
  "resource": "token",
  "used": false,
  "type": "bank_account",
  "created_at": "2026-02-05T10:15:23+00:00"
}
```

### Exemplo cURL

```bash
curl --location 'https://api.gopag.com.br/v1/marketplaces/abc123.../bank_accounts/tokens' \
--header 'Authorization: Bearer SEU_ACCESS_TOKEN' \
--header 'Content-Type: application/json' \
--data '{
  "holder_name": "TECH EXEMPLO COMPANY LTDA",
  "ein": "12345678000190",
  "bank_code": "104",
  "type": "checking",
  "account_number": "664",
  "routing_number": "4928"
}'
```

---

## Passo 2: Associar Token ao Vendedor

### Endpoint

```
POST /v1/marketplaces/{marketplace_id}/bank_accounts
```

### Request Body

```json
{
  "customer": "b9c9b3caba29469691136b77d6d32837",
  "token": "387fc5b1f107468a8ddc3871cef5f0ec"
}
```

### Response: 201 Created

```json
{
  "id": "abc123def456789012345678901234ab",
  "resource": "bank_account",
  "holder_name": "TECH EXEMPLO COMPANY LTDA",
  "taxpayer_id": "12345678000190",
  "bank_code": "104",
  "bank_name": "Caixa Econômica Federal",
  "type": "checking",
  "last4_digits": "0664",
  "account_number": "664",
  "routing_number": "4928",
  "is_active": true,
  "is_verified": false,
  "customer": "b9c9b3caba29469691136b77d6d32837",
  "uri": "/v1/marketplaces/abc123.../bank_accounts/abc123def456789012345678901234ab",
  "created_at": "2026-02-05T10:15:23+00:00",
  "updated_at": "2026-02-05T10:15:23+00:00"
}
```

## Campos do Token (Passo 1)

| Campo | Tipo | Obrigatório | Descrição |
|-------|------|-------------|-----------|
| `holder_name` | string | Sim | Nome do titular da conta (MAIÚSCULAS) |
| `taxpayer_id` | string | Condicional* | CPF (11 dígitos) para pessoa física |
| `ein` | string | Condicional* | CNPJ (14 dígitos) para pessoa jurídica |
| `bank_code` | string | Sim | Código do banco (3 dígitos) |
| `type` | string | Sim | Tipo da conta: `checking` ou `savings` |
| `account_number` | string | Sim | Número da conta (sem dígito) |
| `routing_number` | string | Sim | Agência (sem dígito) |

> **\*** Use `taxpayer_id` (CPF) **OU** `ein` (CNPJ), nunca ambos ao mesmo tempo.

---

## Campos da Associação (Passo 2)

| Campo | Tipo | Obrigatório | Descrição |
|-------|------|-------------|-----------|
| `customer` | string | Sim | ID do vendedor (32 caracteres hexadecimais) |
| `token` | string | Sim | Token gerado no Passo 1 (30 caracteres) |


## Códigos de Banco Principais

| Código | Nome |
|--------|------|
| `001` | Banco do Brasil |
| `033` | Santander |
| `104` | Caixa Econômica Federal |
| `237` | Bradesco |
| `341` | Itaú Unibanco |
| `748` | Sicredi |
| `756` | Sicoob |

---

## Tipos de Conta

- **`checking`** - Conta Corrente (mais comum)
- **`savings`** - Conta Poupança

---

## Segurança

### 🔒 Boas Práticas

1. **Tokens são de uso único** - Cada token só pode ser associado uma vez
2. **Valide CPF/CNPJ** - Certifique-se de que o documento é válido antes de enviar
3. **HTTPS obrigatório** - Todas as requisições devem usar protocolo seguro
4. **Proteja seu Access Token** - Nunca exponha em código cliente

---

## Status da Conta

Após a criação, a conta passa por verificação:

- **`is_active: true`** - Conta ativa e disponível
- **`is_verified: false`** - Aguardando verificação (pode levar até 24h)
- **`is_verified: true`** - Conta verificada e pronta para recebimentos

## Próximos Passos

- [Listar contas bancárias](listar.md)
- [Buscar detalhes de uma conta](detalhes.md)
- [Remover conta bancária](remover.md)

---

## Suporte

Precisa de ajuda? Entre em contato:
- 📧 suporte@gopag.com.br
- 📚 [Documentação Completa](https://docs.gopag.com.br)
