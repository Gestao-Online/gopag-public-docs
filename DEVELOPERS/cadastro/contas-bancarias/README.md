# Contas Bancárias (Bank Accounts)

Gerenciamento completo de contas bancárias dos vendedores no GoPag API.

## Documentação

### Operações Disponíveis

- **[Criar Conta Bancária](criar.md)** - Tokenizar e associar conta ao vendedor
- **[Listar Contas](listar.md)** - Visualizar contas bancárias de um vendedor
- **[Detalhes da Conta](detalhes.md)** - Recuperar informações de uma conta específica
- **[Remover Conta](remover.md)** - Deletar conta bancária do sistema

---

## Visão Geral

As contas bancárias são utilizadas para receber os pagamentos processados através da plataforma. Cada vendedor pode ter múltiplas contas cadastradas:

- **Dados bancários**: Banco, agência, conta, tipo
- **Titular**: Nome e documento (CPF/CNPJ)
- **Tokenização**: Dados sensíveis são protegidos
- **Verificação**: Status de validação da conta

### Fluxo Básico

```
1. Criar Token com Dados Bancários
   ↓
2. Associar Token ao Vendedor
   ↓
3. Validação Automática
   ↓
4. Conta Pronta para Recebimentos
```

---

## Início Rápido

### Criar Token de Conta Bancária

```bash
curl --location 'https://api.gopag.com.br/v1/marketplaces/YOUR_MARKETPLACE_ID/bank_accounts/tokens' \
--header 'Authorization: Bearer YOUR_ACCESS_TOKEN' \
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

### Associar Conta ao Vendedor

```bash
curl --location 'https://api.gopag.com.br/v1/marketplaces/YOUR_MARKETPLACE_ID/bank_accounts' \
--header 'Authorization: Bearer YOUR_ACCESS_TOKEN' \
--header 'Content-Type: application/json' \
--data '{
  "customer": "SELLER_ID",
  "token": "387fc5b1f107468a8ddc3871cef5f0ec"
}'
```

### Listar Contas do Vendedor

```bash
curl --location 'https://api.gopag.com.br/v1/marketplaces/YOUR_MARKETPLACE_ID/sellers/SELLER_ID/bank_accounts' \
--header 'Authorization: Bearer YOUR_ACCESS_TOKEN'
```

---


## Códigos de Bancos Suportados

| Código | Banco |
|--------|-------|
| `001` | Banco do Brasil |
| `033` | Santander |
| `104` | Caixa Econômica Federal |
| `237` | Bradesco |
| `341` | Itaú |
| `748` | Sicredi |
| `756` | Sicoob |

> **Nota**: Para lista completa de bancos verifique a lista atualizada junto ao BACEN

---

## Tipos de Conta

- **`checking`**: Conta Corrente
- **`savings`**: Conta Poupança

---

## Documentação por Pessoa

### Pessoa Física (CPF)
- Use o campo `taxpayer_id` com 11 dígitos
- Conta pode ser individual

### Pessoa Jurídica (CNPJ)
- Use o campo `ein` com 14 dígitos
- Conta deve estar em nome da empresa

---

## Segurança e Boas Práticas

### 🔒 Tokenização
- Dados bancários sensíveis são tokenizados
- Token é de uso único
- Após associação, o token não pode ser reutilizado

### ✅ Validações
- Nome do titular deve corresponder ao documento
- Dados bancários são verificados automaticamente
- Conta inativa não pode receber pagamentos

### 📋 Recomendações
- Sempre valide o status `is_verified` antes de processar pagamentos
- Mantenha apenas contas ativas cadastradas
- Remova contas antigas ou inválidas
- Use metadata para identificar contas (ex: "principal", "backup")

---

## Próximos Passos

1. [Criar sua primeira conta bancária](criar.md)
2. [Listar contas existentes](listar.md)
3. [Gerenciar contas dos vendedores](detalhes.md)

---

## Suporte

Dúvidas? Entre em contato:
- 📧 Email: suporte@gopag.com.br
- 📚 Documentação completa: https://docs.gopag.com.br
- 💬 Chat: Disponível no painel administrativo
