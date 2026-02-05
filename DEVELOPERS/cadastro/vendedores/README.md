# Vendedores (Sellers)

Gerenciamento completo de vendedores no GoPag API.

## Documentação

### Operações Disponíveis

- **[Criar Vendedor](criar.md)** - Cadastrar pessoa física ou jurídica
- **[Buscar por CPF/CNPJ](buscar-cpf-cnpj.md)** - Localizar vendedor usando documento
- **[Listar Vendedores](listar.md)** - Visualizar todos os vendedores
- **[Detalhes do Vendedor](detalhes.md)** - Recuperar e atualizar informações
- **[MCC (Merchant Category Codes)](mcc.md)** - Códigos de categoria de negócio

---

## Visão Geral

Os vendedores (sellers) são os comerciantes que processam pagamentos através da plataforma. Cada vendedor pode ser:

- **Pessoa Física (PF)**: Empresário individual
- **Pessoa Jurídica (PJ)**: Empresas registradas com CNPJ

### Informações do Vendedor

- **Dados pessoais/empresariais**: Nome, documento, contatos
- **Endereço**: Localização física do negócio
- **Dados bancários**: Contas para recebimento
- **MCC**: Categoria do negócio (ramo de atividade)
- **Status**: Pending, active, suspended
- **Metadata**: Campos customizados

### Fluxo Básico

```
1. Criar Vendedor (PF ou PJ)
   ↓
2. Aguardar Aprovação/Análise
   ↓
3. Cadastrar Conta Bancária
   ↓
4. Vendedor Pronto para Receber Pagamentos
```

---

## Início Rápido

### Criar Vendedor Pessoa Física

```bash
curl --location 'https://api.gopag.com.br/v1/marketplaces/YOUR_MARKETPLACE_ID/sellers' \
--header 'Authorization: Bearer YOUR_ACCESS_TOKEN' \
--header 'Content-Type: application/json' \
--data '{
  "taxpayer_id": "12345678900",
  "email": "vendedor@email.com",
  "phone_number": "11987654321",
  "first_name": "João Silva"
}'
```

### Criar Vendedor Pessoa Jurídica

```bash
curl --location 'https://api.gopag.com.br/v1/marketplaces/YOUR_MARKETPLACE_ID/sellers' \
--header 'Authorization: Bearer YOUR_ACCESS_TOKEN' \
--header 'Content-Type: application/json' \
--data '{
  "ein": "12345678000190",
  "email": "contato@empresa.com.br",
  "phone_number": "1133334444",
  "first_name": "Minha Empresa LTDA"
}'
```

### Buscar por CPF

```bash
curl --location 'https://api.gopag.com.br/v1/marketplaces/YOUR_MARKETPLACE_ID/sellers/search?taxpayer_id=12345678900' \
--header 'Authorization: Bearer YOUR_ACCESS_TOKEN'
```

### Buscar por CNPJ

```bash
curl --location 'https://api.gopag.com.br/v1/marketplaces/YOUR_MARKETPLACE_ID/sellers/search?ein=12345678000190' \
--header 'Authorization: Bearer YOUR_ACCESS_TOKEN'
```

---

## Status do Vendedor

| Status | Descrição |
|--------|-----------|
| `pending` | Aguardando análise/documentação |
| `active` | Aprovado e operacional |
| `suspended` | Temporariamente suspenso |
| `rejected` | Não aprovado |

---


## Validações Importantes

### ✅ Documento Único

- Cada CPF ou CNPJ só pode ter **um vendedor ativo** por marketplace
- Use busca antes de criar para evitar duplicatas

### ✅ Email Único

- Email deve ser único no marketplace
- Recomenda-se validação antes do cadastro

---

## Segurança e Compliance

### 🔒 KYC (Know Your Customer)

Vendedores passam por análise que pode incluir:

- Validação de documentos
- Verificação de endereço
- Análise de risco
- Consulta a bureaus de crédito

### 📋 Documentação Requerida

Dependendo do tipo e volume:

- **PF**: RG, CPF, comprovante de endereço
- **PJ**: Contrato social, CNPJ, documentos do owner

### ⚠️ Conformidade

- Sellers devem estar em conformidade com regulamentações locais
- Transações podem ser bloqueadas se houver problemas

---

## Fluxo de Aprovação

```
┌─────────────────┐
│  Criar Seller   │
│   (pending)     │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ Análise KYC     │
│  (automática)   │
└────────┬────────┘
         │
    ┌────┴────┐
    │         │
    ▼         ▼
┌────────┐ ┌──────────┐
│ Active │ │ Rejected │
└────────┘ └──────────┘
```

---

## Boas Práticas

### ✅ Recomendações

1. **Busque antes de criar**: Evite duplicatas consultando por CPF/CNPJ
2. **Valide documentos**: Use bibliotecas para validar CPF/CNPJ antes de enviar
3. **Preencha dados completos**: Quanto mais informações, mais rápida a aprovação
4. **Email válido**: Use email ativo para notificações importantes

## Próximos Passos

1. [Criar seu primeiro vendedor](criar.md)
2. [Buscar vendedor existente](buscar-cpf-cnpj.md)
3. [Listar vendedores do marketplace](listar.md)
4. [Entender códigos MCC](mcc.md)

---

## Suporte

Dúvidas? Entre em contato:
- 📧 Email: suporte@gopag.com.br
- 📚 Documentação completa: https://docs.gopag.com.br
- 💬 Chat: Disponível no painel administrativo
