# Detalhes da Transferência

Recupera os detalhes completos de uma transferência específica.

## Endpoint

```
GET /v1/marketplaces/{marketplace_id}/transfers/{transfer_id}
```

## Parâmetros de URL

| Parâmetro | Tipo | Obrigatório | Descrição |
|-----------|------|-------------|-----------|
| `marketplace_id` | string | Sim | ID do marketplace |
| `transfer_id` | string | Sim | ID da transferência |

## Headers

```
Authorization: Bearer {api_token}
Accept: application/json
```

## Exemplo de Requisição

```bash
curl --request GET \
  --url 'https://api.gopag.com.br/v1/marketplaces/abc123.../transfers/transfer456...' \
  --header 'Authorization: Bearer seu_token_aqui' \
  --header 'Accept: application/json'
```

## Exemplo de Resposta (200 OK)

```json
{
  "id": "transfer456...",
  "resource": "transfer",
  "amount": 50000,
  "original_amount": 52500,
  "currency": "BRL",
  "status": "succeeded",
  "status_detail": "transfer_completed",
  "description": "Transferência automática - Semana 03/2024",
  "recipient": "seller123...",
  "sender": "marketplace456...",
  "confirmed": true,
  "bank_account": {
    "id": "bank789...",
    "resource": "bank_account",
    "account_number": "12345",
    "routing_number": "0678",
    "bank_code": "001",
    "holder_name": "João Silva Comercio LTDA",
    "taxpayer_id": "12345678000190",
    "type": "checking",
    "bank_name": "Banco do Brasil"
  },
  "transfer_date": "2024-01-15",
  "transfer_number": "TRF-2024-00123",
  "type": "automatic",
  "metadata": {
    "batch_id": "BATCH-2024-W03",
    "processing_type": "automatic"
  },
  "location_latitude": null,
  "location_longitude": null,
  "statement_descriptor": "GOPAG*JOAO SILVA",
  "created_at": "2024-01-15T10:00:00Z",
  "updated_at": "2024-01-15T10:05:00Z",
  "uri": "/v1/marketplaces/abc123.../transfers/transfer456..."
}
```

## Campos da Resposta

| Campo | Tipo | Descrição |
|-------|------|-----------|
| `id` | string | ID único da transferência |
| `resource` | string | Tipo do recurso (sempre "transfer") |
| `amount` | integer | Valor líquido transferido em centavos (após taxas) |
| `original_amount` | integer | Valor original antes de taxas em centavos |
| `currency` | string | Moeda utilizada (BRL) |
| `status` | string | Status da transferência |
| `status_detail` | string | Detalhes do status |
| `description` | string | Descrição da transferência |
| `recipient` | string | ID do vendedor que recebe a transferência |
| `sender` | string | ID do marketplace que envia a transferência |
| `confirmed` | boolean | Se a transferência foi confirmada |
| `bank_account` | object | Dados completos da conta bancária |
| `transfer_date` | string | Data em que a transferência foi realizada (YYYY-MM-DD) |
| `transfer_number` | string | Número identificador único da transferência |
| `type` | string | Tipo de transferência (automatic, manual) |
| `metadata` | object | Metadados adicionais personalizados |
| `location_latitude` | string/null | Latitude (se aplicável) |
| `location_longitude` | string/null | Longitude (se aplicável) |
| `statement_descriptor` | string | Descrição que aparece no extrato bancário |
| `created_at` | string | Data de criação (ISO 8601) |
| `updated_at` | string | Data da última atualização (ISO 8601) |
| `uri` | string | URI do recurso |

### Objeto bank_account

| Campo | Tipo | Descrição |
|-------|------|-----------|
| `id` | string | ID da conta bancária |
| `resource` | string | Tipo do recurso (bank_account) |
| `account_number` | string | Número da conta |
| `routing_number` | string | Número da agência |
| `bank_code` | string | Código do banco |
| `holder_name` | string | Nome do titular da conta |
| `taxpayer_id` | string | CPF/CNPJ do titular |
| `type` | string | Tipo de conta (checking, savings) |
| `bank_name` | string | Nome do banco |

### Status Possíveis

| Status | Descrição |
|--------|-----------|
| `pending` | Transferência pendente, aguardando processamento |
| `succeeded` | Transferência concluída com sucesso |
| `failed` | Transferência falhou |
| `canceled` | Transferência cancelada |

## Códigos de Resposta

| Código | Descrição |
|--------|-----------|
| 200 | Transferência recuperada com sucesso |
| 400 | ID de transferência inválido |
| 401 | Não autenticado ou token inválido |
| 403 | Sem permissão para acessar esta transferência |
| 404 | Transferência não encontrada |
| 500 | Erro interno do servidor |

## Exemplo de Resposta de Erro (404)

```json
{
  "type": "not_found",
  "title": "Transfer not found",
  "status": 404,
  "detail": "The requested transfer could not be found"
}
```

## Exemplo de Resposta de Erro (403)

```json
{
  "type": "forbidden",
  "title": "Unauthorized to access this transfer",
  "status": 403,
  "detail": "You do not have permission to access this transfer",
  "trace_id": "abc123def456"
}
```

## Notas Importantes

* A transferência deve pertencer a um vendedor associado ao marketplace autenticado
* Valores monetários são sempre em centavos
* O campo `transactions` contém um resumo das transações. Para detalhes completos, use a rota de [Transações da Transferência](transacoes.md)
* O campo `metadata` pode conter informações personalizadas adicionadas pelo sistema
* A diferença entre `original_amount` e `amount` representa as taxas aplicadas

## Próximos Passos

* Veja [Transações da Transferência](transacoes.md) para obter lista completa de transações
* Consulte [Listar Transferências](listar.md) para buscar múltiplas transferências
* Entenda [Contas Bancárias](../cadastro/contas-bancarias/README.md) para gerenciar dados bancários
