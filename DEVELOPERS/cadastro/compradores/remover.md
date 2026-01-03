# Remover Comprador

Endpoint para remover (deletar) um comprador do sistema.

## Endpoint

```
DELETE /v1/marketplaces/{marketplace_id}/buyers/{buyer_id}
```

## Request

```bash
curl --location --request DELETE 'https://api.gopag.com.br/v1/marketplaces/abc123.../buyers/e4e8c5b569da48b28d896385f5481bcf' \
--header 'Authorization: Bearer SEU_ACCESS_TOKEN'
```

## Response: 200 OK

```json
{
  "id": "e4e8c5b569da48b28d896385f5481bcf",
  "resource": "buyer",
  "deleted": true,
  "deleted_at": "2025-12-21T15:30:00Z"
}
```

## Response: 404 Not Found

```json
{
  "status": 404,
  "detail": "Buyer not found",
  "trace_id": "a1b2c3"
}
```

---

## ⚠️ ATENÇÃO - OPERAÇÃO IRREVERSÍVEL

### O que acontece ao remover um comprador:

✅ **É removido:**
- Cadastro do comprador
- Cartões tokenizados vinculados ao comprador
- Dados pessoais (nome, email, telefone, endereço)
- Metadata customizada

❌ **NÃO é removido:**
- Transações passadas (histórico preservado)
- Registros de auditoria
- Logs de operações

### Impacto em Transações

```
┌─────────────────┐
│ Comprador       │
│ ID: e4e8c5b...  │ ──┐
└─────────────────┘   │
                      │
┌─────────────────┐   │   ┌──────────────────┐
│ Transação 1     │───┼──→│ Histórico        │
│ 2025-01-10      │   │   │ Preservado ✅    │
└─────────────────┘   │   └──────────────────┘
                      │
┌─────────────────┐   │
│ Transação 2     │───┤
│ 2025-02-15      │   │
└─────────────────┘   │
                      │
    [DELETE]          │
        ↓             │
┌─────────────────┐   │
│ Comprador       │   │
│ REMOVIDO ❌     │←──┘
└─────────────────┘
```

---

## Exemplos de Uso

### Remover Comprador Simples

```javascript
async function deleteBuyer(buyerId) {
  const response = await fetch(
    `https://api.gopag.com.br/v1/marketplaces/${marketplaceId}/buyers/${buyerId}`,
    {
      method: 'DELETE',
      headers: {
        'Authorization': `Bearer ${accessToken}`
      }
    }
  );

  if (response.ok) {
    const result = await response.json();
    console.log('Comprador removido:', result.id);
    return result;
  } else {
    throw new Error('Erro ao remover comprador');
  }
}

// Uso
await deleteBuyer('e4e8c5b569da48b28d896385f5481bcf');
```

### Remover com Confirmação

```python
def delete_buyer_with_confirmation(buyer_id):
    """Remove comprador após buscar e confirmar dados"""
    
    # 1. Buscar dados do comprador
    buyer = get_buyer_details(buyer_id)
    
    # 2. Confirmar dados
    print(f"Tem certeza que deseja remover?")
    print(f"Nome: {buyer['first_name']} {buyer['last_name']}")
    print(f"CPF: {buyer['taxpayer_id']}")
    print(f"Email: {buyer['email']}")
    
    confirm = input("Digite 'CONFIRMAR' para prosseguir: ")
    
    if confirm != 'CONFIRMAR':
        print("Operação cancelada")
        return None
    
    # 3. Remover comprador
    url = f'https://api.gopag.com.br/v1/marketplaces/{marketplace_id}/buyers/{buyer_id}'
    headers = {'Authorization': f'Bearer {access_token}'}
    
    response = requests.delete(url, headers=headers)
    
    if response.status_code == 200:
        result = response.json()
        print(f"Comprador removido com sucesso em {result['deleted_at']}")
        return result
    else:
        response.raise_for_status()

# Uso
delete_buyer_with_confirmation('e4e8c5b...')
```

### Verificar Transações Antes de Remover

```php
<?php
function safeBuyerDeletion($buyerId) {
    // 1. Verificar se há transações recentes (últimos 30 dias)
    $recentTransactions = getRecentTransactions($buyerId, 30);
    
    if (count($recentTransactions) > 0) {
        throw new Exception(
            "Não é possível remover: existem " . count($recentTransactions) . 
            " transações nos últimos 30 dias"
        );
    }
    
    // 2. Verificar se há transações pendentes
    $pendingTransactions = getPendingTransactions($buyerId);
    
    if (count($pendingTransactions) > 0) {
        throw new Exception(
            "Não é possível remover: existem transações pendentes"
        );
    }
    
    // 3. Seguro para remover
    return deleteBuyer($buyerId);
}

function deleteBuyer($buyerId) {
    global $marketplaceId, $accessToken;
    
    $url = "https://api.gopag.com.br/v1/marketplaces/{$marketplaceId}/buyers/{$buyerId}";
    
    $ch = curl_init($url);
    curl_setopt($ch, CURLOPT_CUSTOMREQUEST, 'DELETE');
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
    curl_setopt($ch, CURLOPT_HTTPHEADER, [
        'Authorization: Bearer ' . $accessToken
    ]);
    
    $response = curl_exec($ch);
    $httpCode = curl_getinfo($ch, CURLINFO_HTTP_CODE);
    curl_close($ch);
    
    if ($httpCode === 200) {
        return json_decode($response, true);
    } else {
        throw new Exception("Erro ao remover comprador: HTTP {$httpCode}");
    }
}
?>
```

### Remover em Lote (Com Cuidado!)

```javascript
/**
 * Remove múltiplos compradores
 * USE COM EXTREMO CUIDADO!
 */
async function bulkDeleteBuyers(buyerIds, safetyCheck = true) {
  const results = {
    deleted: [],
    failed: [],
    skipped: []
  };

  for (const buyerId of buyerIds) {
    try {
      // Verificação de segurança opcional
      if (safetyCheck) {
        const hasRecentActivity = await checkRecentActivity(buyerId);
        if (hasRecentActivity) {
          results.skipped.push({
            id: buyerId,
            reason: 'Atividade recente detectada'
          });
          continue;
        }
      }

      // Remover comprador
      const result = await deleteBuyer(buyerId);
      results.deleted.push(result);
      
      // Aguardar entre requisições para evitar rate limit
      await sleep(1000);
      
    } catch (error) {
      results.failed.push({
        id: buyerId,
        error: error.message
      });
    }
  }

  return results;
}

// Uso (com muito cuidado!)
const buyersToDelete = ['id1', 'id2', 'id3'];
const results = await bulkDeleteBuyers(buyersToDelete, true);
console.log('Removidos:', results.deleted.length);
console.log('Falhas:', results.failed.length);
console.log('Ignorados:', results.skipped.length);
```

---

## Casos de Uso

### 1. LGPD - Direito ao Esquecimento

```javascript
/**
 * Implementar requisição de exclusão de dados (LGPD)
 */
async function processDataDeletionRequest(taxpayerId, requestReason) {
  try {
    // 1. Buscar comprador por CPF
    const buyer = await searchBuyerByCPF(taxpayerId);
    
    if (!buyer) {
      return { status: 'not_found', message: 'Comprador não encontrado' };
    }

    // 2. Registrar solicitação de exclusão
    await logDeletionRequest({
      buyer_id: buyer.id,
      taxpayer_id: taxpayerId,
      reason: requestReason,
      requested_at: new Date().toISOString()
    });

    // 3. Verificar período de retenção legal
    const retentionPeriod = await checkLegalRetention(buyer.id);
    if (!retentionPeriod.canDelete) {
      return {
        status: 'retention_period',
        message: `Dados devem ser mantidos até ${retentionPeriod.deleteAfter}`,
        reason: retentionPeriod.reason
      };
    }

    // 4. Remover comprador
    const result = await deleteBuyer(buyer.id);

    // 5. Registrar conclusão
    await logDeletionCompleted({
      buyer_id: buyer.id,
      deleted_at: result.deleted_at
    });

    return {
      status: 'deleted',
      message: 'Dados removidos com sucesso',
      deleted_at: result.deleted_at
    };

  } catch (error) {
    await logDeletionError(taxpayerId, error);
    throw error;
  }
}
```

### 2. Limpeza de Dados de Teste

```python
def cleanup_test_buyers():
    """Remove compradores de teste criados durante desenvolvimento"""
    
    # Buscar compradores com metadata indicando ambiente de teste
    test_buyers = get_buyers_by_metadata({'environment': 'test'})
    
    deleted_count = 0
    
    for buyer in test_buyers:
        try:
            delete_buyer(buyer['id'])
            deleted_count += 1
            print(f"Removido comprador de teste: {buyer['id']}")
        except Exception as e:
            print(f"Erro ao remover {buyer['id']}: {str(e)}")
    
    print(f"\nTotal de compradores de teste removidos: {deleted_count}")
    return deleted_count
```

---

## Dicas

### ✅ Boas Práticas

1. **Confirme sempre**: Peça confirmação do usuário antes de deletar
2. **Verifique transações**: Confira se há transações pendentes
3. **Registre a ação**: Mantenha log de quem e quando deletou
4. **Considere inativação**: Em vez de deletar, considere marcar como inativo
5. **LGPD**: Implemente processo adequado para direito ao esquecimento

### ⚠️ Cuidados

- **Operação irreversível**: Não há como recuperar dados deletados
- **Cartões removidos**: Tokens de cartão vinculados serão deletados
- **Transações preservadas**: Histórico de transações não é afetado
- **Sem cascade**: Não remove automaticamente recursos relacionados

### 💡 Alternativa: Soft Delete

Em vez de remover permanentemente, considere implementar "soft delete":

```javascript
// Em vez de DELETE, use PATCH para marcar como inativo
async function deactivateBuyer(buyerId) {
  return await updateBuyer(buyerId, {
    metadata: {
      active: false,
      deactivated_at: new Date().toISOString(),
      deactivation_reason: 'User request'
    }
  });
}
```

---

## Erros Comuns

### Comprador Não Encontrado

```json
{
  "status": 404,
  "detail": "Buyer not found",
  "trace_id": "a1b2c3"
}
```

**Solução**: Verifique se o `buyer_id` está correto ou se o comprador já foi removido.

### Sem Permissão

```json
{
  "status": 403,
  "detail": "Insufficient permissions to delete buyer",
  "trace_id": "d4e5f6"
}
```

**Solução**: Verifique se o token de acesso tem permissão de escrita.

---

## Próximos Passos

- [Criar Novo Comprador](criar.md)
- [Buscar por CPF/CNPJ](buscar-cpf-cnpj.md)
- [Atualizar Detalhes](detalhes.md)
- [Documentação LGPD](#) (em breve)
