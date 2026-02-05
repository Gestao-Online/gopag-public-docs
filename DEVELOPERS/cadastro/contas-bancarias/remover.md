# Remover Conta Bancária

Endpoint para deletar uma conta bancária do sistema.

## Endpoint

```
DELETE /v1/marketplaces/{marketplace_id}/bank_accounts/{bank_account_id}
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
  "deleted": true
}
```
---

## Comportamento

### ⚠️ Ação Permanente

- A remoção é **irreversível**
- A conta não pode ser recuperada após a exclusão
- Registros históricos são mantidos para auditoria

### 📋 Requisitos

Antes de remover uma conta, certifique-se de que:

- ✅ Não há pagamentos pendentes associados
- ✅ Não há recebíveis futuros vinculados
- ✅ O vendedor possui outra conta ativa (se necessário)

---

## Boas Práticas

### ✅ Recomendações

1. **Confirme antes de remover**: Implemente dupla confirmação na UI
2. **Valide dependências**: Verifique recebíveis e pagamentos pendentes
3. **Mantenha histórico**: Registre a remoção em seu sistema
4. **Notifique o vendedor**: Envie email/notificação sobre a remoção
5. **Alternativa disponível**: Garanta que há outra conta ativa antes de remover

---

## Próximos Passos

- [Criar nova conta bancária](criar.md)
- [Listar contas existentes](listar.md)
- [Buscar detalhes de uma conta](detalhes.md)

---

## Suporte

Precisa de ajuda?
- 📧 suporte@gopag.com.br
- 📚 [Documentação Completa](https://docs.gopag.com.br)
