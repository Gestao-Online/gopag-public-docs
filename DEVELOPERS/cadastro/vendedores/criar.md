# Criar Vendedor

Cadastre vendedores pessoa física (PF) ou pessoa jurídica (PJ) no marketplace.

---

## Endpoint

```
POST /v1/marketplaces/{marketplace_id}/sellers
```

---

## Autenticação

```
Authorization: Bearer {access_token}
```

---

## Tipos de Vendedor

A API suporta dois tipos de vendedores, diferenciados pelo campo de documento enviado:

| Tipo | Campo Documento | Tamanho | Descrição |
|------|----------------|---------|-----------|
| **Pessoa Física (PF)** | `taxpayer_id` | 11 dígitos | CPF do comerciante |
| **Pessoa Jurídica (PJ)** | `ein` | 14 dígitos | CNPJ da empresa |

> **Importante**: Use **apenas um** dos campos (`taxpayer_id` ou `ein`) por requisição. A presença do campo determina o tipo de vendedor.

---

## Criar Vendedor Pessoa Física (PF)

### Request

```http
POST /v1/marketplaces/{marketplace_id}/sellers
Authorization: Bearer {access_token}
Content-Type: application/json

{
  "taxpayer_id": "12345678900",
  "email": "suporte@gopag.com.br",
  "phone_number": "6236024409",
  "first_name": "Jhon exemplo"
}
```

### Campos Obrigatórios (PF)

| Campo | Tipo | Descrição |
|-------|------|-----------|
| `taxpayer_id` | string | CPF (11 dígitos, apenas números) |
| `email` | string | Email do vendedor |
| `phone_number` | string | Telefone (DDD + número) |
| `first_name` | string | Nome completo |

### Response (201 Created)

```json
{
    "kyc_href":"https://cloud.identifique.se/app-ui/t/t/c7357fd6-4881-11f0-a158-597c4a62c31e",
    "detail":"Seller creation process initiated successfully [WIU2C6]"
}
```

---

## Criar Vendedor Pessoa Jurídica (PJ)

### Request

```http
POST /v1/marketplaces/{marketplace_id}/sellers
Authorization: Bearer {access_token}
Content-Type: application/json

{
  "ein": "58753292000113",
  "email": "suporte@gopag.com.br",
  "phone_number": "6236024409",
  "first_name": "Gopag Soluções Tecnológicas em Pagamentos Ltda"
}
```

### Campos Obrigatórios (PJ)

| Campo | Tipo | Descrição |
|-------|------|-----------|
| `ein` | string | CNPJ (14 dígitos, apenas números) |
| `email` | string | Email da empresa |
| `phone_number` | string | Telefone (DDD + número) |
| `first_name` | string | Razão social ou nome fantasia |

### Response (201 Created)

```json
{
    "kyc_href":"https://cloud.identifique.se/app-ui/t/t/c7357fd6-4881-11f0-a158-597c4a62c31e",
    "detail":"Seller creation process initiated successfully [WIU2C6]"
}
```

---

## Status do Vendedor

Após criação, o vendedor passa por análise:

| Status | Descrição |
|--------|-----------|
| `pending` | Aguardando análise/documentação |
| `active` | Aprovado e operacional |
| `suspended` | Temporariamente suspenso |
| `rejected` | Não aprovado (motivo fornecido) |

### Fluxo de Aprovação

```
POST /sellers
     ↓
[pending] → Análise KYC
     ↓
[active] ou [rejected]
```

---

## Validações

### CPF (taxpayer_id)

- ✅ Exatamente 11 dígitos numéricos
- ✅ CPF válido (algoritmo de validação)
- ✅ Único na GOPAG
- ❌ Não pode ser CPF em blacklist

### CNPJ (ein)

- ✅ Exatamente 14 dígitos numéricos
- ✅ CNPJ válido (algoritmo de validação)
- ✅ Único na GOPAG
- ❌ Não pode estar inativo na Receita Federal

### Email

- ✅ Formato válido (RFC 5322)
- ✅ Único na GOPAG
- ✅ Domínio existente

### Telefone

- ✅ 10-11 dígitos (DDD + número)
- ✅ DDD válido do Brasil
- ✅ Formato: `11987654321` (sem formatação)

---

## Próximos Passos

Após criar o vendedor:

1. **Aguardar Aprovação**: Monitorar status via polling
2. **Testar Transação**: Realizar uma transação de teste

### Recursos Relacionados

- [Buscar Vendedor por CPF/CNPJ](buscar-cpf-cnpj.md)
- [Listar Vendedores](listar.md)
- [Detalhes do Vendedor](detalhes.md)
- [Códigos MCC](mcc.md)
- [Criar Conta Bancária](../contas-bancarias/criar.md)

---

## Suporte

Dúvidas? Entre em contato:
- 📧 Email: suporte@gopag.com.br
- 📚 Documentação: https://docs.gopag.com.br
