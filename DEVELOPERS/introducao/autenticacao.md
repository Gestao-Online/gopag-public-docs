# Autenticação

A GoPag API utiliza autenticação de dois fatores para garantir a segurança das comunicações:

1. **Bearer Token (OAuth 2.0)**: Obtido via Portal GoPag ou enviado pela GoPag (Entre em contato para solicitar)
2. **Certificado mTLS (ICP-Brasil)**: Autenticação bidirecional com certificado digital

> **📋 CREDENCIAMENTO NECESSÁRIO**
>
> Para integrar com a GoPag API, você precisará de:
> - **Bearer Token**: Exportado pelo Portal GoPag ou enviado pelo seu consultor
> - **Certificado digital ICP-Brasil**: e-CPF ou e-CNPJ para mTLS
> 
> Entre em contato com nossa equipe de suporte para iniciar o processo de credenciamento.
>
> 📧 Email: suporte@gopag.com.br

## Fluxo de Autenticação

```
┌──────────┐                                      ┌─────────────┐
│  Cliente │                                      │  GoPag API  │
└────┬─────┘                                      └──────┬──────┘
     │                                                   │
     │  1. Conexão TLS + Certificado mTLS (ICP-Brasil)  │
     ├──────────────────────────────────────────────────>│
     │                                                   │
     │  2. Validação do Certificado                     │
     │<──────────────────────────────────────────────────┤
     │                                                   │
     │  3. Requisição API + Bearer Token                │
     ├──────────────────────────────────────────────────>│
     │                                                   │
     │  4. Validação Token + marketplace_id             │
     │                                                   │
     │  5. Resposta                                     │
     │<──────────────────────────────────────────────────┤
     │                                                   │
```

## Passo 1: Obter Bearer Token

### Via Portal GoPag

1. Acesse o Portal GoPag
2. Navegue até **Configurações > API**
3. Clique em **Exportar Token**
4. Copie o Bearer Token gerado

### Via Consultor

O Bearer Token pode ser enviado diretamente pelo seu consultor GoPag via e-mail ou canal seguro.

### Características do Token

- **Tipo**: Bearer
- **Validade**: Configurável (geralmente longa duração)
- **Uso**: Incluir no header `Authorization` de todas as requisições

```
Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9...
```

## Passo 2: Configurar Certificado mTLS

### Recebimento do Certificado

Após o credenciamento, você receberá da equipe GoPag:

1. **Certificado Digital ICP-Brasil** (`client.crt`)
   - Certificado público X.509 formato PEM
   - Tipo: e-CPF (pessoa física) ou e-CNPJ (pessoa jurídica)
   - Contém o `certSerialNumber` que deve corresponder ao seu `marketplace_id`

2. **Chave Privada** (`client.key`)
   - Chave privada RSA 2048 bits ou superior
   - **NUNCA compartilhe esta chave**

3. **CA Certificate** (`ca.crt`)
   - Certificado da Autoridade Certificadora

**⚠️ IMPORTANTE**: O certificado é emitido exclusivamente pela equipe GoPag após análise e aprovação do credenciamento.

### Instalação

#### Linux/MacOS

```bash
# Criar diretório para certificados
sudo mkdir -p /etc/ssl/gopag

# Copiar certificados
sudo cp client.crt /etc/ssl/gopag/
sudo cp client.key /etc/ssl/gopag/
sudo cp ca.crt /etc/ssl/gopag/

# Definir permissões
sudo chmod 644 /etc/ssl/gopag/client.crt
sudo chmod 644 /etc/ssl/gopag/ca.crt
sudo chmod 600 /etc/ssl/gopag/client.key
sudo chown app-user:app-group /etc/ssl/gopag/client.key
```

#### Verificar Certificado

```bash
# Ver informações do certificado
openssl x509 -in /etc/ssl/gopag/client.crt -text -noout

# Verificar serial number (deve corresponder ao marketplace_id)
openssl x509 -in /etc/ssl/gopag/client.crt -serial -noout
```

### Configuração por Linguagem

#### cURL

```bash
curl https://api.gopag.com.br/v1/marketplaces/abc123.../sellers \
  --cert /etc/ssl/gopag/client.crt \
  --key /etc/ssl/gopag/client.key \
  --cacert /etc/ssl/gopag/ca.crt \
  -H "Authorization: Bearer SEU_TOKEN"
```

#### PHP (com cURL)

```php
$ch = curl_init('https://api.gopag.com.br/v1/marketplaces/abc123.../sellers');

curl_setopt_array($ch, [
    CURLOPT_RETURNTRANSFER => true,
    CURLOPT_SSLCERT => '/etc/ssl/gopag/client.crt',
    CURLOPT_SSLKEY => '/etc/ssl/gopag/client.key',
    CURLOPT_CAINFO => '/etc/ssl/gopag/ca.crt',
    CURLOPT_HTTPHEADER => [
        'Authorization: Bearer ' . $accessToken,
        'Content-Type: application/json'
    ]
]);

$response = curl_exec($ch);
curl_close($ch);
```

#### Node.js (com axios)

```javascript
const fs = require('fs');
const https = require('https');
const axios = require('axios');

const httpsAgent = new https.Agent({
  cert: fs.readFileSync('/etc/ssl/gopag/client.crt'),
  key: fs.readFileSync('/etc/ssl/gopag/client.key'),
  ca: fs.readFileSync('/etc/ssl/gopag/ca.crt')
});

const response = await axios.get(
  'https://api.gopag.com.br/v1/marketplaces/abc123.../sellers',
  {
    httpsAgent,
    headers: {
      'Authorization': `Bearer ${accessToken}`
    }
  }
);
```

#### Python (com requests)

```python
import requests

response = requests.get(
    'https://api.gopag.com.br/v1/marketplaces/abc123.../sellers',
    cert=('/etc/ssl/gopag/client.crt', '/etc/ssl/gopag/client.key'),
    verify='/etc/ssl/gopag/ca.crt',
    headers={'Authorization': f'Bearer {access_token}'}
)
```

#### Java (com OkHttp)

```java
OkHttpClient client = new OkHttpClient.Builder()
    .sslSocketFactory(sslContext.getSocketFactory(), trustManager)
    .build();

Request request = new Request.Builder()
    .url("https://api.gopag.com.br/v1/marketplaces/abc123.../sellers")
    .header("Authorization", "Bearer " + accessToken)
    .build();

Response response = client.newCall(request).execute();
```

## Passo 3: Usar nas Requisições

### Header de Autorização

Inclua o Bearer Token em todas as requisições:

```http
GET /v1/marketplaces/abc123.../sellers HTTP/1.1
Host: api.gopag.com.br
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGc...
Accept: application/json
```

### Validação do Marketplace ID

**CRÍTICO**: O `certSerialNumber` do certificado mTLS **DEVE** corresponder ao `marketplace_id` na URL.

```
✅ Correto:
- certSerialNumber: marketplace-abc123
- URL: /v1/marketplaces/marketplace-abc123/sellers

❌ Erro 401:
- certSerialNumber: marketplace-abc123
- URL: /v1/marketplaces/marketplace-xyz789/sellers
```

## Tratamento de Erros de Autenticação

### 401 Unauthorized

```json
{
  "type": "https://httpstatuses.com/401",
  "title": "Unauthorized",
  "status": 401,
  "detail": "Invalid or expired access token"
}
```

**Causas comuns:**
- Bearer Token inválido ou expirado
- Token não fornecido no header

**Solução:**
- Verifique se o token está correto
- Solicite um novo token via Portal GoPag ou consultor

### 401 Unauthorized (Marketplace)

```json
{
  "type": "https://httpstatuses.com/401",
  "title": "Unauthorized",
  "status": 401,
  "detail": "Unauthorized"
}
```

**Causas comuns:**
- `certSerialNumber` não corresponde ao `marketplace_id`
- Certificado mTLS inválido ou expirado

**Solução:**
- Verifique se o `marketplace_id` na URL está correto
- Confirme que seu certificado ainda está válido
- Entre em contato com suporte se necessário

### 403 Forbidden

```json
{
  "type": "https://httpstatuses.com/403",
  "title": "Forbidden",
  "status": 403,
  "detail": "Insufficient permissions"
}
```

**Causas comuns:**
- Tentando acessar recurso de outro marketplace
- Permissões insuficientes

**Solução:**
- Verifique se está acessando apenas recursos do seu marketplace
- Entre em contato com suporte se necessário

## Segurança - Melhores Práticas

### ✅ Fazer

- Armazenar certificados e chaves em locais seguros
- Usar variáveis de ambiente para Bearer Token
- Validar certificados SSL do servidor
- Usar HTTPS em todas as comunicações
- Implementar timeout em requisições
- Logar tentativas de autenticação (sem expor credenciais)

### ❌ Não Fazer

- Commitar certificados, chaves ou tokens no Git
- Expor Bearer Token em código cliente (frontend)
- Compartilhar credenciais entre ambientes
- Ignorar erros de validação SSL
- Armazenar tokens sem proteção
- Usar certificados expirados
- Fazer hard-code de credenciais

## Ambientes

### Homologação

```
Base URL: https://api-stage.gopag.com.br
```

- Use credenciais de homologação
- Certificado de homologação
- Sem impacto em produção

### Produção

```
Base URL: https://api.gopag.com.br
```

- Use credenciais de produção
- Certificado de produção
- Transações reais

**⚠️ NUNCA use credenciais de produção em homologação!**

## Próximos Passos

- [Testar sua Autenticação](./testando.md)
- [Entender Códigos de Erro](./codigos-erro.md)
- [Começar a Integração](../vendedores/listar.md)
