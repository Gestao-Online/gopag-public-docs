# Buscar Terminal

## Visão Geral

Retorna os detalhes completos de um terminal específico, incluindo metadados, status e informações do dispositivo.

## Endpoint

```
GET /v1/marketplaces/{marketplace_id}/sellers/{seller_id}/terminals/{terminal_id}
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
| `terminal_id` | integer | Sim | ID numérico do terminal |

## Exemplos de Requisição

### cURL

```bash
curl -X GET https://app.gopag.com.br/v1/marketplaces/HOMOLOG/sellers/00771bc0349847108f54e200dbc6c325/terminals/30 \
  -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGc..."
```

### JavaScript

```javascript
async function getTerminal(sellerId, terminalId) {
  const response = await fetch(
    `https://app.gopag.com.br/v1/marketplaces/HOMOLOG/sellers/${sellerId}/terminals/${terminalId}`,
    {
      headers: {
        'Authorization': `Bearer ${accessToken}`
      }
    }
  );

  if (!response.ok) {
    const error = await response.json();
    throw new Error(error.detail || `Erro: ${response.status}`);
  }

  return await response.json();
}

// Uso
getTerminal(
  '00771bc0349847108f54e200dbc6c325',
  '30'
)
  .then(terminal => {
    console.log('Terminal encontrado:');
    console.log(`- ID: ${terminal.id}`);
    console.log(`- Unique ID: ${terminal.unique_id}`);
    console.log(`- Tipo: ${terminal.type}`);
    console.log(`- Status: ${terminal.status}`);
    console.log(`- Descrição: ${terminal.description}`);
  })
  .catch(error => console.error('Erro:', error.message));
```

### Python

```python
import requests

def get_terminal(seller_id, terminal_id, access_token):
    """Busca detalhes de um terminal específico"""
    
    url = f"https://app.gopag.com.br/v1/marketplaces/HOMOLOG/sellers/{seller_id}/terminals/{terminal_id}"
    
    headers = {
        "Authorization": f"Bearer {access_token}"
    }
    
    response = requests.get(url, headers=headers)
    
    if response.status_code == 200:
        return response.json()
    elif response.status_code == 404:
        raise Exception("Terminal não encontrado")
    elif response.status_code == 401:
        raise Exception("Não autorizado a acessar este terminal")
    else:
        response.raise_for_status()

# Uso
try:
    terminal = get_terminal(
        seller_id="00771bc0349847108f54e200dbc6c325",
        terminal_id="30",
        access_token="eyJ0eXAiOiJKV1Qi..."
    )
    
    print(f"Terminal ID: {terminal['id']}")
    print(f"Unique ID: {terminal['unique_id']}")
    print(f"Status: {terminal['status']}")
    print(f"Tipo: {terminal['type']}")
    print(f"Descrição: {terminal['description']}")
    
except Exception as e:
    print(f"Erro: {e}")
```

### PHP

```php
<?php

function getTerminal($sellerId, $terminalId, $accessToken) {
    $url = "https://app.gopag.com.br/v1/marketplaces/HOMOLOG/sellers/{$sellerId}/terminals/{$terminalId}";
    
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
    } elseif ($httpCode === 404) {
        throw new Exception("Terminal não encontrado");
    } elseif ($httpCode === 401) {
        throw new Exception("Não autorizado");
    } else {
        throw new Exception("Erro ao buscar terminal: HTTP {$httpCode}");
    }
}

// Uso
try {
    $terminal = getTerminal(
        "00771bc0349847108f54e200dbc6c325",
        "30",
        "eyJ0eXAiOiJKV1Qi..."
    );
    
    echo "Terminal ID: " . $terminal['id'] . "\n";
    echo "Unique ID: " . $terminal['unique_id'] . "\n";
    echo "Status: " . $terminal['status'] . "\n";
    echo "Tipo: " . $terminal['type'] . "\n";
    echo "Descrição: " . $terminal['description'] . "\n";
    
    if (isset($terminal['serial_number'])) {
        echo "Número de Série: " . $terminal['serial_number'] . "\n";
    }
    
} catch (Exception $e) {
    echo "Erro: " . $e->getMessage() . "\n";
}
```

### Ruby

```ruby
require 'net/http'
require 'json'

def get_terminal(seller_id, terminal_id, access_token)
  uri = URI("https://app.gopag.com.br/v1/marketplaces/HOMOLOG/sellers/#{seller_id}/terminals/#{terminal_id}")
  
  request = Net::HTTP::Get.new(uri)
  request['Authorization'] = "Bearer #{access_token}"
  
  response = Net::HTTP.start(uri.hostname, uri.port, use_ssl: true) do |http|
    http.request(request)
  end
  
  case response.code
  when '200'
    JSON.parse(response.body)
  when '404'
    raise "Terminal não encontrado"
  when '401'
    raise "Não autorizado"
  else
    raise "Erro: #{response.code} - #{response.body}"
  end
end

# Uso
begin
  terminal = get_terminal(
    '00771bc0349847108f54e200dbc6c325',
    '30',
    'eyJ0eXAiOiJKV1Qi...'
  )
  
  puts "Terminal ID: #{terminal['id']}"
  puts "Unique ID: #{terminal['unique_id']}"
  puts "Status: #{terminal['status']}"
  puts "Tipo: #{terminal['type']}"
  puts "Descrição: #{terminal['description']}"
  
rescue => e
  puts "Erro: #{e.message}"
end
```

## Resposta de Sucesso

**Status:** `200 OK`

```json
{
  "resource": "terminal",
  "id": 30,
  "type": "paxs920",
  "description": "POS s920",
  "status": "paired",
  "os": null,
  "latitude": null,
  "longitude": null,
  "metadata": null,
  "unique_id": "69375802",
  "device_model": null,
  "app_version": null,
  "created_at": "2026-01-02 23:36:51",
  "updated_at": "2026-01-02 23:37:11",
  "serial_number": null
}
```

## Campos da Resposta

| Campo | Tipo | Descrição |
|-------|------|-----------|
| `resource` | string | Sempre `"terminal"` |
| `id` | integer | ID numérico do terminal |
| `unique_id` | string | Identificador único do dispositivo |
| `type` | string | Tipo do terminal (`paxs920`, `ttpIos`, etc) |
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

## Erros Comuns

### 404 - Terminal Não Encontrado

```json
{
  "status": 404,
  "title": "Not Found",
  "detail": "Terminal not found [trace_id_456]",
  "trace_id": "trace_id_456"
}
```

**Causas:**
- Terminal ID inválido
- Terminal não pertence ao seller especificado
- Terminal foi removido

**Solução:** Verifique o terminal_id e seller_id.

### 401 - Não Autorizado

```json
{
  "status": 401,
  "title": "Unauthorized",
  "detail": "Unauthorized to access this seller [trace_id_789]",
  "trace_id": "trace_id_789"
}
```

**Causa:** O seller não pertence ao partner autenticado.

**Solução:** Verifique as credenciais e o seller_id.

### 400 - Parâmetros Inválidos

```json
{
  "status": 400,
  "title": "Bad Request",
  "detail": "Invalid terminal ID format"
}
```

**Causa:** O terminal_id não tem o formato correto (32 caracteres hexadecimais).

**Solução:** Valide o formato do ID antes de enviar.

## Casos de Uso

### 1. Verificar Status do Terminal

```javascript
async function checkTerminalStatus(sellerId, terminalId) {
  try {
    const terminal = await getTerminal(sellerId, terminalId);
    
    if (terminal.status === 'active') {
      console.log('✅ Terminal ativo e pronto para uso');
      return true;
    } else {
      console.warn(`⚠️ Terminal não está ativo: ${terminal.status}`);
      return false;
    }
  } catch (error) {
    console.error('❌ Erro ao verificar terminal:', error.message);
    return false;
  }
}
```

### 2. Monitorar Última Conexão

```python
from datetime import datetime, timedelta

def check_terminal_connection(seller_id, terminal_id, access_token):
    """Verifica se o terminal conectou recentemente"""
    
    terminal = get_terminal(seller_id, terminal_id, access_token)
    
    if 'last_connection' in terminal['metadata']:
        last_conn = datetime.fromisoformat(
            terminal['metadata']['last_connection'].replace('+00:00', '')
        )
        now = datetime.utcnow()
        diff = now - last_conn
        
        if diff > timedelta(hours=24):
            print(f"⚠️ Terminal offline há {diff.days} dias")
            return False
        else:
            print(f"✅ Terminal conectado há {diff.seconds // 3600} horas")
            return True
    else:
        print("⚠️ Informação de conexão não disponível")
        return None
```

### 3. Dashboard de Terminais

```php
<?php

function getTerminalsDashboard($sellerId, $terminalIds, $accessToken) {
    $dashboard = [
        'active' => 0,
        'inactive' => 0,
        'total' => count($terminalIds),
        'by_type' => []
    ];
    
    foreach ($terminalIds as $terminalId) {
        try {
            $terminal = getTerminal($sellerId, $terminalId, $accessToken);
            
            // Contar por status
            if ($terminal['status'] === 'active') {
                $dashboard['active']++;
            } else {
                $dashboard['inactive']++;
            }
            
            // Contar por tipo
            $type = $terminal['type'];
            if (!isset($dashboard['by_type'][$type])) {
                $dashboard['by_type'][$type] = 0;
            }
            $dashboard['by_type'][$type]++;
            
        } catch (Exception $e) {
            error_log("Erro ao buscar terminal {$terminalId}: " . $e->getMessage());
        }
    }
    
    return $dashboard;
}
```

## Boas Práticas

### 1. Cache de Terminais

```javascript
const terminalCache = new Map();
const CACHE_TTL = 5 * 60 * 1000; // 5 minutos

async function getTerminalCached(sellerId, terminalId) {
  const cacheKey = `${sellerId}_${terminalId}`;
  const cached = terminalCache.get(cacheKey);
  
  if (cached && Date.now() < cached.expiry) {
    return cached.data;
  }
  
  const terminal = await getTerminal(sellerId, terminalId);
  
  terminalCache.set(cacheKey, {
    data: terminal,
    expiry: Date.now() + CACHE_TTL
  });
  
  return terminal;
}
```

### 2. Tratamento Robusto de Erros

```python
def get_terminal_safe(seller_id, terminal_id, access_token, retries=3):
    """Busca terminal com retry e tratamento de erros"""
    
    for attempt in range(retries):
        try:
            return get_terminal(seller_id, terminal_id, access_token)
        except requests.exceptions.ConnectionError:
            if attempt < retries - 1:
                wait = 2 ** attempt  # Exponential backoff
                time.sleep(wait)
                continue
            raise
        except requests.exceptions.HTTPError as e:
            if e.response.status_code in [404, 401]:
                # Não faz retry para estes erros
                raise
            elif attempt < retries - 1:
                time.sleep(2 ** attempt)
                continue
            raise
```

### 3. Logging e Auditoria

```php
function getTerminalWithLogging($sellerId, $terminalId, $accessToken) {
    $startTime = microtime(true);
    
    try {
        error_log(sprintf(
            "[TERMINAL_GET] Buscando terminal - Seller: %s, Terminal: %s",
            $sellerId,
            $terminalId
        ));
        
        $terminal = getTerminal($sellerId, $terminalId, $accessToken);
        
        $duration = (microtime(true) - $startTime) * 1000;
        error_log(sprintf(
            "[TERMINAL_GET] Sucesso - Terminal: %s, Status: %s, Tempo: %.2fms",
            $terminal['serial_number'],
            $terminal['status'],
            $duration
        ));
        
        return $terminal;
        
    } catch (Exception $e) {
        $duration = (microtime(true) - $startTime) * 1000;
        error_log(sprintf(
            "[TERMINAL_GET] Erro - Seller: %s, Terminal: %s, Erro: %s, Tempo: %.2fms",
            $sellerId,
            $terminalId,
            $e->getMessage(),
            $duration
        ));
        
        throw $e;
    }
}
```

## Próximos Passos

- [Listar Terminais](listar.md) - Veja todos os terminais do vendedor
- [Parear Terminal](parear.md) - Adicione novos terminais
- [Criar Transação](../transacoes/criar/mpos.md) - Processe pagamentos com o terminal
