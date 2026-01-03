# Compradores (Buyers)

Gerenciamento completo de compradores no GoPag API.

## Documentação

### Operações CRUD

- **[Criar Comprador](criar.md)** - Cadastrar novo comprador no sistema
- **[Buscar por CPF/CNPJ](buscar-cpf-cnpj.md)** - Localizar comprador usando documento
- **[Detalhes do Comprador](detalhes.md)** - Recuperar e atualizar informações
- **[Remover Comprador](remover.md)** - Deletar comprador do sistema

---

## Visão Geral

Os compradores (buyers) são os clientes finais que realizam compras através da plataforma. Cada comprador possui:

- **Dados pessoais**: Nome, email, telefone, CPF/CNPJ
- **Endereço**: Informações completas de localização
- **Cartões tokenizados**: Métodos de pagamento salvos
- **Metadata**: Campos customizados para sua aplicação

### Fluxo Básico

```
1. Criar Comprador
   ↓
2. Tokenizar Cartão (opcional)
   ↓
3. Criar Transação
   ↓
4. Reutilizar em próximas compras
```

---

## Início Rápido

### Criar Primeiro Comprador

```bash
curl --location 'https://api.gopag.com.br/v1/marketplaces/YOUR_MARKETPLACE_ID/buyers' \
--header 'Authorization: Bearer YOUR_ACCESS_TOKEN' \
--header 'Content-Type: application/json' \
--data '{
  "first_name": "Maria",
  "last_name": "Santos",
  "email": "maria@email.com",
  "taxpayer_id": "12345678901"
}'
```

### Buscar por CPF

```bash
curl --location 'https://api.gopag.com.br/v1/marketplaces/YOUR_MARKETPLACE_ID/buyers/search?taxpayer_id=12345678901' \
--header 'Authorization: Bearer YOUR_ACCESS_TOKEN'
```

### Atualizar Email

```bash
curl --location --request PATCH 'https://api.gopag.com.br/v1/marketplaces/YOUR_MARKETPLACE_ID/buyers/BUYER_ID' \
--header 'Authorization: Bearer YOUR_ACCESS_TOKEN' \
--header 'Content-Type: application/json' \
--data '{"email": "novo@email.com"}'
```

---

## Casos de Uso Comuns

### 1. Checkout com Comprador Novo

```javascript
// Verificar se comprador já existe
let buyer = await searchBuyerByCPF(cpf);

if (!buyer) {
  // Criar novo comprador
  buyer = await createBuyer({
    first_name: 'Maria',
    last_name: 'Santos',
    email: 'maria@email.com',
    taxpayer_id: cpf
  });
}

// Criar transação
const transaction = await createTransaction({
  customer: buyer.id,
  amount: 10000,
  payment_type: 'credit'
});
```

### 2. Compra Recorrente (Assinatura)

```python
# Buscar comprador existente
buyer = search_buyer_by_cpf('12345678901')

# Criar cobrança mensal usando customer_id
transaction = create_transaction({
    'customer': buyer['id'],  # Usa cartão tokenizado
    'amount': 4990,
    'currency': 'BRL',
    'description': 'Assinatura Premium - Mensal',
    'capture': True
})
```

### 3. Atualização de Cadastro

```php
<?php
// Cliente mudou de email
$buyer = updateBuyer($buyerId, [
    'email' => 'novo@email.com',
    'metadata' => [
        'email_updated_at' => date('c'),
        'email_updated_by' => 'customer_portal'
    ]
]);
?>
```

---

## Estrutura de Dados

### Objeto Buyer Completo

```json
{
  "id": "e4e8c5b569da48b28d896385f5481bcf",
  "resource": "buyer",
  "first_name": "Maria",
  "last_name": "Santos",
  "email": "maria.santos@email.com",
  "phone_number": "11987654321",
  "taxpayer_id": "12345678901",
  "birthdate": "1990-07-15",
  "address": {
    "line1": "Rua das Palmeiras, 456",
    "line2": "Apto 789",
    "line3": "",
    "neighborhood": "Jardins",
    "city": "São Paulo",
    "state": "SP",
    "postal_code": "01418000",
    "country_code": "BR"
  },
  "metadata": {
    "customer_level": "gold",
    "origin": "mobile_app"
  },
  "created_at": "2025-12-21T10:00:00Z",
  "updated_at": "2025-12-21T10:00:00Z"
}
```

---

## Boas Práticas

### ✅ Recomendado

- **Sempre busque antes de criar**: Evite duplicatas verificando CPF primeiro
- **Armazene o buyer_id**: Guarde para reutilizar em transações futuras
- **Use metadata**: Adicione informações customizadas relevantes para seu negócio
- **Valide dados**: Verifique email e CPF antes de enviar
- **Tokenize cartões**: Use `usage: 'reusable'` para vendas recorrentes

### ❌ Evite

- Criar compradores duplicados sem verificar CPF
- Armazenar dados sensíveis em metadata
- Atualizar todos os campos quando precisa mudar apenas um
- Deletar compradores sem verificar transações pendentes
- Formatar CPF com pontos e traços (use apenas números)

---

## Próximos Passos

### Documentação Relacionada

- [Criar Transação com Comprador](../../transacoes/criar/api/cartao.md)
- [Tokenização de Cartões](../../transacoes/tokenizar.md)
- [Gestão de Vendedores](../vendedores/listar.md)
- [API Reference](../../introducao/autenticacao.md)

### Tutoriais

- [Implementar Checkout Completo](#)
- [Sistema de Assinaturas](#)
- [LGPD e Privacidade](#)

---

## Precisa de Ajuda?

- 📧 Email: suporte@gopag.com.br
- 📚 [Documentação Completa](../../README.md)
- 💬 [Portal do Desenvolvedor](#)
