# Detalhes da Conta Bancária

Endpoint para recuperar informações detalhadas de uma conta bancária específica.

## Endpoint

```
GET /v1/marketplaces/{marketplace_id}/bank_accounts/{bank_account_id}
```

## Parâmetros de URL

| Parâmetro | Tipo | Descrição |
|-----------|------|-----------|
| `marketplace_id` | string | ID do marketplace |
| `bank_account_id` | string | ID da conta bancária (32 caracteres hexadecimais) |

## Response: 200 OK

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
  "is_verified": true,
  "customer": "b9c9b3caba29469691136b77d6d32837",
  "uri": "/v1/marketplaces/abc123.../bank_accounts/abc123def456789012345678901234ab",
  "created_at": "2026-02-05T10:15:23+00:00",
  "updated_at": "2026-02-05T10:15:23+00:00"
}
```

## Campos da Resposta

| Campo | Tipo | Descrição |
|-------|------|-----------|
| `id` | string | ID único da conta bancária |
| `resource` | string | Tipo do recurso (`bank_account`) |
| `holder_name` | string | Nome do titular da conta |
| `taxpayer_id` | string | CPF/CNPJ do titular (apenas retornado se foi o documento usado) |
| `bank_code` | string | Código do banco (3 dígitos) |
| `bank_name` | string | Nome do banco |
| `type` | string | Tipo da conta: `checking` ou `savings` |
| `last4_digits` | string | Últimos 4 dígitos da conta |
| `account_number` | string | Número da conta (pode estar parcialmente mascarado) |
| `routing_number` | string | Número da agência |
| `is_active` | boolean | Se a conta está ativa |
| `is_verified` | boolean | Se a conta foi verificada |
| `customer` | string | ID do vendedor dono da conta |
| `uri` | string | URI do recurso |
| `created_at` | string | Data de criação (ISO 8601) |
| `updated_at` | string | Data da última atualização (ISO 8601) |

---

## Erros

### Conta Não Encontrada

```json
{
  "status_code": 404,
  "type": "not_found",
  "message": "Conta bancária não encontrada"
}
```

**Status Code**: `404 Not Found`

**Possíveis causas**:
- ID da conta inválido
- Conta foi removida
- Conta pertence a outro marketplace

### ID Inválido

```json
{
  "status_code": 400,
  "type": "validation_error",
  "message": "ID da conta bancária deve ter 32 caracteres hexadecimais"
}
```

**Status Code**: `400 Bad Request`

### Não Autorizado

```json
{
  "status_code": 401,
  "type": "unauthorized",
  "message": "Token de acesso inválido ou expirado"
}
```

**Status Code**: `401 Unauthorized`

---

## Segurança

### 🔒 Dados Sensíveis

Por segurança, os seguintes dados são **mascarados ou omitidos**:

- ✓ Número completo da conta (apenas últimos 4 dígitos)
- ✓ CPF/CNPJ pode ser omitido dependendo das permissões
- ✓ Metadata sensível é filtrada

### ✅ Boas Práticas

1. **Cache com cuidado**: Dados de contas bancárias podem ser sensíveis
2. **Validar permissões**: Certifique-se de que o usuário tem acesso a essa conta
3. **Logs seguros**: Não registre números completos de contas em logs
4. **Exibição parcial**: Sempre mostre apenas dados mascarados na interface

---

## Informações Adicionais

### Bancos Suportados

O campo `bank_name` retorna o nome completo do banco com base no `bank_code`:

| Código | Nome |
|--------|------|
| `001` | Banco do Brasil |
| `033` | Santander |
| `104` | Caixa Econômica Federal |
| `237` | Bradesco |
| `341` | Itaú Unibanco |
| `748` | Sicredi |
| `756` | Sicoob |

### Tipos de Conta

- **`checking`** - Conta Corrente (mais comum para empresas)
- **`savings`** - Conta Poupança (mais comum para PF)

---

## Próximos Passos

- [Criar nova conta bancária](criar.md)
- [Listar todas as contas](listar.md)
- [Remover conta bancária](remover.md)

---

## Suporte

Precisa de ajuda?
- 📧 suporte@gopag.com.br
- 📚 [Documentação Completa](https://docs.gopag.com.br)
