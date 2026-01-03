# Listar Terminais

## Visão Geral

Lista todos os terminais associados a um vendedor específico. Suporta paginação.

## Endpoint

```
GET /v1/marketplaces/{marketplace_id}/sellers/{seller_id}/terminals
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
| `seller_id` | string | Sim | ID do vendedor (32 caracteres hexadecimais) |

## Parâmetros de Query (Opcionais)

| Parâmetro | Tipo | Padrão | Descrição |
|-----------|------|--------|-----------|
| `limit` | integer | 20 | Número de registros por página (1-100) |
| `offset` | integer | 0 | Número de registros a pular (para paginação) |

## Exemplos de Requisição

### cURL - Listagem Básica

```bash
curl -X GET "https://app.gopag.com.br/v1/marketplaces/HOMOLOG/sellers/00771bc0349847108f54e200dbc6c325/terminals" \
  -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGc..."
```

### cURL - Com Paginação

```bash
curl -X GET "https://app.gopag.com.br/v1/marketplaces/HOMOLOG/sellers/00771bc0349847108f54e200dbc6c325/terminals?limit=50&offset=0" \
  -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGc..."
```

### JavaScript

```javascript
async function listTerminals(sellerId, options = {}) {
  const params = new URLSearchParams({
    limit: options.limit || 20,
    offset: options.offset || 0
  });

  const response = await fetch(
    `https://app.gopag.com.br/v1/marketplaces/HOMOLOG/sellers/${sellerId}/terminals?${params}`,
    {
      headers: {
        'Authorization': `Bearer ${accessToken}`
      }
    }
  );

  if (!response.ok) {
    throw new Error(`Erro: ${response.status}`);
  }

  return await response.json();
}

// Uso: Listar terminais com paginação
listTerminals('00771bc0349847108f54e200dbc6c325', { 
  limit: 50,
  offset: 0
})
  .then(data => {
    console.log(`Total: ${data.total}`);
    data.items.forEach(terminal => {
      console.log(`- ${terminal.serial_number} (${terminal.type})`);
    });
  })
  .catch(error => console.error('Erro:', error));
```

### Python

```python
import requests

def list_terminals(seller_id, access_token, limit=20, offset=0):
    """Lista terminais de um vendedor"""
    
    url = f"https://app.gopag.com.br/v1/marketplaces/HOMOLOG/sellers/{seller_id}/terminals"
    
    headers = {
        "Authorization": f"Bearer {access_token}"
    }
    
    params = {
        "limit": limit,
        "offset": offset
    }
    
    response = requests.get(url, headers=headers, params=params)
    response.raise_for_status()
    
    return response.json()

# Uso: Listar terminais com paginação
try:
    result = list_terminals(
        seller_id="00771bc0349847108f54e200dbc6c325",
        access_token="eyJ0eXAiOiJKV1Qi...",
        limit=50,
        offset=0
    )
    
    print(f"Total de terminais: {result['total']}")
    print(f"Retornados nesta página: {result['query_count']}")
    print(f"Há mais páginas: {result['has_more']}")
    
    for terminal in result['items']:
        print(f"\n- ID: {terminal['id']}")
        print(f"  Número de Série: {terminal['serial_number']}")
        print(f"  Tipo: {terminal['type']}")
        print(f"  Status: {terminal['status']}")
        
except requests.exceptions.HTTPError as e:
    print(f"Erro HTTP: {e}")
    print(f"Detalhes: {e.response.json()}")
```

### PHP

```php
<?php

function listTerminals($sellerId, $accessToken, $filters = []) {
    $queryParams = http_build_query([
        'limit' => $filters['limit'] ?? 20,
        'offset' => $filters['offset'] ?? 0
    ]);
    
    $url = "https://app.gopag.com.br/v1/marketplaces/HOMOLOG/sellers/{$sellerId}/terminals?{$queryParams}";
    
    $ch = curl_init($url);
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
    curl_setopt($ch, CURLOPT_HTTPHEADER, [
        "Authorization: Bearer {$accessToken}"
    ]);
    
    $response = curl_exec($ch);
    $httpCode = curl_getinfo($ch, CURLINFO_HTTP_CODE);
    curl_close($ch);
    
    if ($httpCode === 200) {
        return json_decode($response, true);
    } else {
        throw new Exception("Erro ao listar terminais: HTTP {$httpCode}");
    }
}

// Uso: Listar terminais com paginação
try {
    $result = listTerminals(
        "00771bc0349847108f54e200dbc6c325",
        "eyJ0eXAiOiJKV1Qi...",
        [
            'limit' => 100,
            'offset' => 0
        ]
    );
    
    echo "Total de terminais: " . $result['total'] . "\n";
    
    foreach ($result['items'] as $terminal) {
        echo "- " . $terminal['serial_number'] . " (" . $terminal['type'] . ")\n";
    }
    
} catch (Exception $e) {
    echo "Erro: " . $e->getMessage() . "\n";
}
```

## Resposta de Sucesso

**Status:** `200 OK`

```json
{
  "items": [
    {
      "resource": "terminal",
      "id": 25,
      "unique_id": "C73D9704-766C-43AB-BFF8-0244829F0EDD",
      "type": "ttpIos",
      "description": "Paulo - iPhone15,2",
      "status": "unpaired",
      "os": "iOS",
      "device_model": "iPhone15,2",
      "app_version": "1.0.1+48",
      "latitude": null,
      "longitude": null,
      "serial_number": null,
      "metadata": null,
      "created_at": "2025-12-22 15:37:39",
      "updated_at": "2025-12-29 17:18:47"
    },
    {
      "resource": "terminal",
      "id": 30,
      "unique_id": "69375802",
      "type": "paxs920",
      "description": "POS s920",
      "status": "paired",
      "os": null,
      "device_model": null,
      "app_version": null,
      "latitude": null,
      "longitude": null,
      "serial_number": null,
      "metadata": null,
      "created_at": "2026-01-02 23:36:51",
      "updated_at": "2026-01-02 23:37:11"
    }
  ],
  "resource": "terminal",
  "has_more": false,
  "limit": 20,
  "offset": 0,
  "total": 2,
  "uri": "/v1/marketplaces/HOMOLOG/sellers/00771bc0349847108f54e200dbc6c325/terminals"
}
```

## Campos da Resposta

| Campo | Tipo | Descrição |
|-------|------|-----------|
| `items` | array | Lista de terminais |
| `resource` | string | Sempre `"terminal"` |
| `has_more` | boolean | Indica se há mais páginas disponíveis |
| `limit` | integer | Número de registros por página |
| `offset` | integer | Número de registros pulados |
| `total` | integer | Total de terminais encontrados |
| `uri` | string | URI da requisição |

### Campos do Terminal (em items)

| Campo | Tipo | Descrição |
|-------|------|-----------|
| `id` | integer | ID interno do terminal |
| `unique_id` | string | Identificador único do dispositivo |
| `resource` | string | Sempre `"terminal"` |
| `type` | string | Tipo do terminal (`ttpIos`, `paxs920`, etc) |
| `description` | string | Descrição do terminal |
| `status` | string | Status (`paired`, `unpaired`, `active`, `inactive`) |
| `os` | string\|null | Sistema operacional (iOS, Android, etc) |
| `device_model` | string\|null | Modelo do dispositivo |
| `app_version` | string\|null | Versão do aplicativo |
| `latitude` | number\|null | Latitude da última localização |
| `longitude` | number\|null | Longitude da última localização |
| `serial_number` | string\|null | Número de série do dispositivo |
| `metadata` | object\|null | Metadados adicionais |
| `created_at` | string | Data de criação (Y-m-d H:i:s) |
| `updated_at` | string | Data da última atualização (Y-m-d H:i:s) |

## Paginação

### Exemplo: Buscar Todas as Páginas

```javascript
async function getAllTerminals(sellerId) {
  const allTerminals = [];
  let offset = 0;
  const limit = 50;
  let hasMore = true;

  while (hasMore) {
    const data = await listTerminals(sellerId, { limit, offset });
    allTerminals.push(...data.items);
    
    hasMore = data.has_more;
    offset += limit;
    
    console.log(`Carregados ${allTerminals.length} de ${data.total} terminais`);
  }

  return allTerminals;
}

// Uso
getAllTerminals('00771bc0349847108f54e200dbc6c325')
  .then(terminals => {
    console.log(`Total final: ${terminals.length} terminais`);
  });
```

## Erros Comuns

### 400 - Seller ID Inválido

```json
{
  "status": 400,
  "title": "Bad Request",
  "detail": "Seller ID is required in the route"
}
```

**Solução:** Forneça um seller_id válido (32 caracteres hexadecimais).

### 401 - Não Autorizado

```json
{
  "status": 401,
  "title": "Unauthorized",
  "detail": "Unauthorized to access this seller [trace_id_123]",
  "trace_id": "trace_id_123"
}
```

**Solução:** Verifique se o seller pertence ao partner autenticado.

## Boas Práticas

### 1. Implementar Cache

```javascript
class TerminalsCache {
  constructor(ttl = 300000) { // 5 minutos
    this.cache = new Map();
    this.ttl = ttl;
  }

  get(key) {
    const item = this.cache.get(key);
    if (!item || Date.now() > item.expiry) {
      this.cache.delete(key);
      return null;
    }
    return item.data;
  }

  set(key, data) {
    this.cache.set(key, {
      data,
      expiry: Date.now() + this.ttl
    });
  }
}

const cache = new TerminalsCache();

async function listTerminalsWithCache(sellerId, options) {
  const cacheKey = `terminals_${sellerId}_${JSON.stringify(options)}`;
  
  const cached = cache.get(cacheKey);
  if (cached) return cached;
  
  const data = await listTerminals(sellerId, options);
  cache.set(cacheKey, data);
  
  return data;
}
```

### 2. Usar Retry com Backoff

```python
import time
from requests.adapters import HTTPAdapter
from urllib3.util.retry import Retry

def get_session_with_retry():
    session = requests.Session()
    retry = Retry(
        total=3,
        backoff_factor=1,
        status_forcelist=[429, 500, 502, 503, 504]
    )
    adapter = HTTPAdapter(max_retries=retry)
    session.mount("https://", adapter)
    return session

# Uso
session = get_session_with_retry()
response = session.get(url, headers=headers, params=params)
```

### 3. Logging e Monitoramento

```php
$startTime = microtime(true);

try {
    $terminals = listTerminals($sellerId, $token, $filters);
    
    $duration = microtime(true) - $startTime;
    error_log(sprintf(
        "Terminais listados com sucesso - Seller: %s, Total: %d, Tempo: %.2fms",
        $sellerId,
        $terminals['total'],
        $duration * 1000
    ));
    
} catch (Exception $e) {
    error_log(sprintf(
        "Erro ao listar terminais - Seller: %s, Erro: %s",
        $sellerId,
        $e->getMessage()
    ));
}
```

## Próximos Passos

- [Buscar Terminal](buscar.md) - Obtenha detalhes de um terminal específico
- [Parear Terminal](parear.md) - Adicione novos terminais ao vendedor
- [Criar Transação](../transacoes/criar/mpos.md) - Processe pagamentos
