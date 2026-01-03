
# Testando Pagamentos

Para realizar testes, utilize o marketplace de homologação:

- **Marketplace ID**: `HOMOLOG`

- **Base URL**: `https://app-stage.gopag.com.br`

## Payment Locales

No ambiente de homologação, estão disponíveis para testes:

✅ **Cartão via API** - Transações diretas com dados do cartão  - **Marketplace ID**: `HOMOLOG`

✅ **Link de Pagamento** - Geração de links para pagamento

⚠️ **Limitações importantes:**

- **PIX e Boleto**: Não estão totalmente funcionais em homologação. Não realize pagamento real.

- **Tap to Pay (NFC)**: Disponível apenas em produção (Os push notifications não chegarão)

- **MPOS**: Disponível apenas em produção 

- **PINPAD**: Disponível apenas em produção


## Testando em homologação (Link de pagamento e cartão de crédito)

### Cartões de Teste - Aprovação

Os seguintes números de cartão podem ser usados para simular transações **bem-sucedidas**:

| Número | Bandeira |
| :--- | :--- |
| 4539003370725497 | Visa \(Digitada\) |
| 4761340000000035 | Visa \(Chip & PIN\) |
| 4716588836362104 | Visa \(Crédito\) |
| 4532650104137832 | Visa Electron \(Crédito\) |
| 5356066320271893 | MasterCard \(Digitada\) |
| 5201561050024014 | MasterCard \(Chip & PIN\) |
| 5577270004286630 | MasterCard \(Crédito\) |
| 5138692036125449 | MasterCard \(Crédito\) |

### Cartões de Teste - Cenários de Erro

Use estes números "mágicos" para simular diferentes respostas de erro:

| Número | Bandeira |
| :--- | :--- |
| 6011457819940087 | A transação será recusada com um código de "card\_declined". |
| 4929710426637678 | A transação será recusada com um código "card\_declined". |
| 4710426743216178 | A transação será recusada com um código "service\_request\_timeout". |

### Exemplo de Uso

```bash
# Teste de aprovação
curl -X POST https://app-stage.gopag.com.br/v1/marketplaces/HOMOLOG/transactions \
  -H "Authorization: Bearer {seu_token}" \
  -H "Content-Type: application/json" \
  -d '{
    "payment_type": "credit",
    "on_behalf_of": "{seller_id}",
    "amount": 10000,
    "currency": "BRL",
    "description": "Teste de pagamento",
    "card": {
      "card_number": "4539003370725497",
      "holder_name": "TESTE APROVADO",
      "expiration_month": "12",
      "expiration_year": "2028",
      "security_code": "123"
    }
  }'

# Teste de recusa
curl -X POST https://app-stage.gopag.com.br/v1/marketplaces/HOMOLOG/transactions \
  -H "Authorization: Bearer {seu_token}" \
  -H "Content-Type: application/json" \
  -d '{
    "payment_type": "credit",
    "on_behalf_of": "{seller_id}",
    "amount": 5000,
    "currency": "BRL",
    "description": "Teste de recusa",
    "card": {
      "card_number": "4929710426637678",
      "holder_name": "TESTE RECUSADO",
      "expiration_month": "12",
      "expiration_year": "2028",
      "security_code": "123"
    }
  }'
```

## Boas Práticas para Testes

1. **Use sempre o ambiente de homologação para testes de cartões** - Nunca use cartões de teste em produção

2. **Teste cenários de sucesso e falha** - Use os cartões específicos para cada cenário

3. **Valide webhooks** - Configure URLs de teste para receber notificações

4. **Teste diferentes valores** - Valores pequenos, grandes, fracionados

5. **Simule timeout** - Use o cartão `4710426743216178` para testar comportamento em timeout

6. **Teste parcelamento** - Valide diferentes números de parcelas


## Limitações do Ambiente de Homologação

- ❌ Transações não geram movimentação financeira real

- ❌ PIX e Boleto não estão totalmente funcionais (não realize o pagamento dos códigos de barras ou QRCodes gerados)

- ❌ Tap to Pay, MPOS e PINPAD disponíveis apenas em produção

- ⚠️ Webhooks podem ter delay diferente de produção


## Testando em Produção (MPOS, PINPAD, Tap to Pay)

Para testar terminais físicos e Tap to Pay, você precisará trabalhar no ambiente de **produção**, pois estes payment locales não estão disponíveis em homologação.

⚠️ **IMPORTANTE**: As transações em produção são reais e geram movimentação financeira.

**Passos para testar:**

1. **Solicitar acesso ao ambiente de produção**

2. **Parear terminais reais** (MPOS/PINPAD) ou dispositivos (Tap to Pay)

3. **Realizar transações de teste com valores baixos** (ex: R$ 1,00)

4. **Monitorar as primeiras transações** com atenção

5. **Validar o fluxo completo** incluindo webhooks e recebimentos


## Suporte

Em caso de dúvidas durante os testes:
- Consulte a [documentação completa](../introducao/visao-geral.md)
- Verifique os [códigos de erro](../introducao/codigos-erro.md)
- Entre em contato com o suporte técnico
