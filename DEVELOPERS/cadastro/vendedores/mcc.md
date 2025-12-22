# Códigos MCC (Merchant Category Codes)

Os códigos MCC (Merchant Category Code) classificam o tipo de negócio do vendedor. São utilizados por adquirentes e bandeiras para identificar a categoria do estabelecimento comercial.

## Endpoint

```
GET /v1/marketplaces/{marketplace_id}/mcc
```

## Request

```bash
curl --location 'https://api.gopag.com.br/v1/marketplaces/abc123.../mcc' \--header 'Authorization: Bearer SEU_ACCESS_TOKEN'
```

## Response

```json
{
  "resource": "list",
  "items": [
    {
      "code": "5411",
      "description": "Supermercados e Mercearias"
    },
    {
      "code": "5732",
      "description": "Lojas de Eletrônicos"
    },
    {
      "code": "5812",
      "description": "Restaurantes e Lanchonetes"
    },
    {
      "code": "5999",
      "description": "Lojas Diversas - Varejo"
    },
    {
      "code": "7230",
      "description": "Salões de Beleza e Barbearias"
    },
    {
      "code": "7299",
      "description": "Serviços Pessoais Diversos"
    },
    {
      "code": "8011",
      "description": "Médicos e Clínicas"
    }
  ]
}
```

## MCCs Comuns

| Código | Descrição |
|--------|-----------|
| 5411 | Supermercados e Mercearias |
| 5541 | Postos de Gasolina |
| 5732 | Lojas de Eletrônicos |
| 5812 | Restaurantes e Lanchonetes |
| 5912 | Farmácias |
| 5999 | Lojas Diversas - Varejo |
| 7230 | Salões de Beleza e Barbearias |
| 7299 | Serviços Pessoais Diversos |
| 8011 | Médicos e Clínicas |
| 8021 | Dentistas |
| 8099 | Profissionais de Saúde |

## Importância do MCC

O código MCC é importante para:

- ✅ **Taxas de intercâmbio**: Diferentes MCCs podem ter taxas diferentes
- ✅ **Análise de risco**: Algumas categorias são consideradas de maior risco
- ✅ **Programas de benefícios**: Categorias específicas podem ter cashback
- ✅ **Compliance**: Necessário para regulamentações do setor

## Selecionando o MCC Correto

Ao cadastrar um vendedor, escolha o MCC que melhor descreve a atividade principal:

❌ **Errado**: Loja de roupas usando MCC 5999 (genérico)
✅ **Correto**: Loja de roupas usando MCC 5651 (Lojas de Vestuário Familiar)

## Próximos Passos

- [Listar Vendedores](./listar.md)
- [Buscar Vendedor](./buscar-cpf-cnpj.md)
- [Detalhes do Vendedor](./detalhes.md)
