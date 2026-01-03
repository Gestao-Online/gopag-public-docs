# Parear Terminal

## Visão Geral

O pareamento vincula um novo terminal (mPOS, PINPAD, Tap to Pay) a um vendedor específico. Este processo é necessário antes que o terminal possa processar transações.

## Endpoint

```
POST /v1/marketplaces/{marketplace_id}/terminals/pairing
```

## Autenticação

Requer token OAuth2 do tipo `partner`.

```
Authorization: Bearer {access_token}
```

## Parâmetros da URL

| Parâmetro | Tipo | Obrigatório | Descrição |
|-----------|------|-------------|-----------|
| `marketplace_id` | string | Sim | ID do marketplace (ex: HOMOLOG, PROD) |

## Corpo da Requisição

| Campo | Tipo | Obrigatório | Descrição |
|-------|------|-------------|-----------|
| `seller` | string | Sim | ID do vendedor (32 caracteres hexadecimais) |
| `marketplace_id` | boolean | Sim | Deve ser `true` para validação |
| `token` | string | Sim | Token de pareamento do terminal |
| `isStaging` | boolean | Não | `true` para ambiente de testes (padrão: `false`) |

## Exemplo de Requisição

### cURL

```bash
curl -X POST https://app.gopag.com.br/v1/marketplaces/HOMOLOG/terminals/pairing \
  -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGc..." \
  -H "Content-Type: application/json" \
  -d '{
    "seller": "00771bc0349847108f54e200dbc6c325",
    "marketplace_id": true,
    "token": "MPOS_TOKEN_ABC123XYZ",
    "isStaging": false
  }'
```

### JavaScript

```javascript
const pairTerminal = async (sellerId, pairingToken) => {
  try {
    const response = await fetch(
      'https://app.gopag.com.br/v1/marketplaces/HOMOLOG/terminals/pairing',
      {
        method: 'POST',
        headers: {
          'Authorization': `Bearer ${accessToken}`,
          'Content-Type': 'application/json'
        },
        body: JSON.stringify({
          seller: sellerId,
          marketplace_id: true,
          token: pairingToken,
          isStaging: false
        })
      }
    );

    if (!response.ok) {
      const error = await response.json();
      throw new Error(error.detail || 'Erro ao parear terminal');
    }

    return await response.json();
  } catch (error) {
    console.error('Erro no pareamento:', error.message);
    throw error;
  }
};

// Uso
pairTerminal('00771bc0349847108f54e200dbc6c325', 'MPOS_TOKEN_ABC123XYZ')
  .then(terminal => console.log('Terminal pareado:', terminal.id))
  .catch(error => console.error('Erro:', error));
```

### Python

```python
import requests

def pair_terminal(seller_id, pairing_token, access_token):
    """Pareia um terminal a um vendedor"""
    
    url = "https://app.gopag.com.br/v1/marketplaces/HOMOLOG/terminals/pairing"
    
    headers = {
        "Authorization": f"Bearer {access_token}",
        "Content-Type": "application/json"
    }
    
    payload = {
        "seller": seller_id,
        "marketplace_id": True,
        "token": pairing_token,
        "isStaging": False
    }
    
    response = requests.post(url, json=payload, headers=headers)
    response.raise_for_status()
    
    return response.json()

# Uso
try:
    terminal = pair_terminal(
        seller_id="00771bc0349847108f54e200dbc6c325",
        pairing_token="MPOS_TOKEN_ABC123XYZ",
        access_token="eyJ0eXAiOiJKV1Qi..."
    )
    
    print(f"Terminal pareado com sucesso: {terminal['id']}")
    print(f"Número de série: {terminal['serial_number']}")
    print(f"Status: {terminal['status']}")
    
except requests.exceptions.HTTPError as e:
    print(f"Erro HTTP: {e}")
    print(f"Detalhes: {e.response.json()}")
except Exception as e:
    print(f"Erro: {e}")
```

### PHP

```php
<?php

function pairTerminal($sellerId, $pairingToken, $accessToken) {
    $url = "https://app.gopag.com.br/v1/marketplaces/HOMOLOG/terminals/pairing";
    
    $data = [
        'seller' => $sellerId,
        'marketplace_id' => true,
        'token' => $pairingToken,
        'isStaging' => false
    ];
    
    $ch = curl_init($url);
    curl_setopt($ch, CURLOPT_POST, true);
    curl_setopt($ch, CURLOPT_POSTFIELDS, json_encode($data));
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
    curl_setopt($ch, CURLOPT_HTTPHEADER, [
        "Authorization: Bearer {$accessToken}",
        "Content-Type: application/json"
    ]);
    
    $response = curl_exec($ch);
    $httpCode = curl_getinfo($ch, CURLINFO_HTTP_CODE);
    curl_close($ch);
    
    if ($httpCode === 201) {
        return json_decode($response, true);
    } else {
        $error = json_decode($response, true);
        throw new Exception($error['detail'] ?? "Erro ao parear terminal");
    }
}

// Uso
try {
    $terminal = pairTerminal(
        "00771bc0349847108f54e200dbc6c325",
        "MPOS_TOKEN_ABC123XYZ",
        "eyJ0eXAiOiJKV1Qi..."
    );
    
    echo "Terminal pareado: " . $terminal['id'] . "\n";
    echo "Tipo: " . $terminal['type'] . "\n";
    echo "Status: " . $terminal['status'] . "\n";
    
} catch (Exception $e) {
    echo "Erro: " . $e->getMessage() . "\n";
}
```

## Resposta de Sucesso

**Status:** `201 Created`

```json
{
  "id": "a1b2c3d4e5f6789012345678901234ab",
  "resource": "terminal",
  "seller": "00771bc0349847108f54e200dbc6c325",
  "serial_number": "PAX-SN-123456",
  "status": "active",
  "type": "mpos",
  "metadata": {
    "device_model": "PAX A920",
    "firmware_version": "1.0.5",
    "manufacturer": "PAX Technology"
  },
  "created_at": "2025-12-29T10:30:00+00:00",
  "updated_at": "2025-12-29T10:30:00+00:00"
}
```

## Erros Comuns

### 400 - Token Inválido

```json
{
  "status": 400,
  "title": "Bad Request",
  "detail": "Invalid pairing token [trace_id_abc]",
  "trace_id": "trace_id_abc"
}
```

**Causa:** O token de pareamento é inválido ou já foi usado.

**Solução:** Obtenha um novo token de pareamento do terminal.

### 401 - Não Autorizado

```json
{
  "status": 401,
  "title": "Unauthorized",
  "detail": "Unauthorized to access this seller [trace_id_def]",
  "trace_id": "trace_id_def"
}
```

**Causa:** O seller não pertence ao partner autenticado.

**Solução:** Verifique se o seller_id está correto e pertence ao seu partner.

### 409 - Terminal Já Pareado

```json
{
  "status": 409,
  "title": "Conflict",
  "detail": "Terminal already paired to another seller [trace_id_ghi]",
  "trace_id": "trace_id_ghi"
}
```

**Causa:** O terminal já está vinculado a outro vendedor.

**Solução:** Remova o terminal do vendedor atual antes de parear novamente.

## Validações

- **Seller ID**: Deve ter exatamente 32 caracteres hexadecimais
- **Token**: Obrigatório e de uso único
- **Marketplace ID**: Deve ser `true`
- **Partner**: Deve ter permissão `allowPos`, `allowPinpad` ou `allowTapToPay` dependendo do tipo de terminal

## Boas Práticas

1. **Valide o token antes de enviar**: Certifique-se de que o token é válido e não expirou
2. **Trate erros adequadamente**: Implemente retry logic para erros temporários (5xx)
3. **Armazene o terminal_id**: Guarde o ID retornado para futuras consultas
4. **Monitore o status**: Verifique se o terminal ficou ativo após o pareamento
5. **Use logging**: Registre todas as tentativas de pareamento para auditoria

## Próximos Passos

- [Listar Terminais](listar.md) - Consulte todos os terminais do vendedor
- [Buscar Terminal](buscar.md) - Obtenha detalhes de um terminal específico
- [Criar Transação](../transacoes/criar/mpos.md) - Processe pagamentos com o terminal
