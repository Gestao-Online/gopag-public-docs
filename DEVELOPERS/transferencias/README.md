# Transferências

## Visão Geral

As transferências representam a movimentação de fundos das transações processadas para a conta bancária do vendedor. Este módulo permite consultar e gerenciar as transferências realizadas.

## O que são Transferências?

Transferências são o processo de envio dos valores recebidos nas transações para a conta bancária cadastrada do vendedor. Elas são geradas automaticamente pelo sistema de acordo com as regras de split e cronograma de repasse configurados.

## Características Principais

* **Consulta de Transferências**: Liste todas as transferências de um vendedor
* **Detalhes da Transferência**: Obtenha informações completas sobre uma transferência específica
* **Transações Relacionadas**: Visualize quais transações compõem cada transferência
* **Rastreabilidade**: Acompanhe o status e histórico de cada transferência

## Status de Transferências

Uma transferência pode estar em um dos seguintes status:

| Status | Descrição |
|--------|-----------|
| `pending` | Transferência pendente, aguardando processamento |
| `succeeded` | Transferência realizada com sucesso |
| `failed` | Transferência falhou (ex: dados bancários inválidos) |
| `canceled` | Transferência cancelada |

## Estrutura de uma Transferência

```json
{
  "id": "abc123...",
  "resource": "transfer",
  "amount": 10000,
  "currency": "BRL",
  "status": "succeeded",
  "description": "Transferência automática",
  "recipient": "seller123...",
  "bank_account": {
    "id": "bank456...",
    "resource": "bank_account",
    "account_number": "12345",
    "routing_number": "001",
    "holder_name": "João Silva"
  },
  "transfer_date": "2024-01-15",
  "transfer_number": "TRF-2024-001",
  "created_at": "2024-01-15T10:00:00Z",
  "updated_at": "2024-01-15T10:05:00Z"
}
```

## Rotas Disponíveis

* [Listar Transferências](listar.md) - Lista todas as transferências de um vendedor
* [Detalhes da Transferência](detalhes.md) - Obtém detalhes de uma transferência específica
* [Transações da Transferência](transacoes.md) - Lista as transações que compõem uma transferência

## Próximos Passos

* Consulte [Listar Transferências](listar.md) para começar a consultar transferências
* Veja [Detalhes da Transferência](detalhes.md) para entender a estrutura completa
* Explore [Transações da Transferência](transacoes.md) para rastreabilidade
