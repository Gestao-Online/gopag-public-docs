# Códigos de Erro

A GoPag API utiliza códigos de status HTTP padronizados e retorna detalhes adicionais no corpo da resposta seguindo o padrão RFC 7807 (Problem Details).

## Estrutura de Erro

Todas as respostas de erro seguem este formato:

```json
{
  "type": "https://httpstatuses.com/400",
  "title": "Bad Request",
  "status": 400,
  "detail": "Field payment_type is required",
  "trace_id": "a1b2c3"
}
```

### Campos

| Campo | Tipo | Descrição |
|-------|------|-----------|
| `type` | string | URI que identifica o tipo de erro |
| `title` | string | Resumo legível do erro |
| `status` | integer | Código de status HTTP |
| `detail` | string | Descrição detalhada do erro |
| `trace_id` | string | ID único para rastreamento (6 caracteres) |

## Códigos de Status HTTP

### 2xx - Sucesso

#### 200 OK
Requisição bem-sucedida.

```json
{
  "resource": "seller",
  "id": "abc123...",
  "status": "active"
}
```

#### 201 Created
Recurso criado com sucesso.

```json
{
  "resource": "transaction",
  "id": "def456...",
  "status": "succeeded",
  "created_at": "2025-12-21T10:30:00Z"
}
```

### 4xx - Erros do Cliente

#### 400 Bad Request
Requisição inválida ou mal formatada.

**Exemplos:**

```json
{
  "type": "https://httpstatuses.com/400",
  "title": "Bad Request",
  "status": 400,
  "detail": "Field payment_type is required",
  "trace_id": "a1b2c3"
}
```

```json
{
  "type": "https://httpstatuses.com/400",
  "title": "Bad Request",
  "status": 400,
  "detail": "Invalid on_behalf_of (seller_id) format",
  "trace_id": "d4e5f6"
}
```

```json
{
  "type": "https://httpstatuses.com/400",
  "title": "Bad Request",
  "status": 400,
  "detail": "Unexpected fields in request [g7h8i9]",
  "trace_id": "g7h8i9",
  "skipped_fields": {
    "invalid_field": "value",
    "source.card.wrong_field": "value"
  }
}
```

**Causas comuns:**
- Campo obrigatório ausente
- Formato de campo inválido (ex: ID não hexadecimal de 32 chars)
- Campos inesperados na requisição
- Valor fora do intervalo permitido
- Tipo de dado incorreto

**Solução:**
- Verifique os campos obrigatórios
- Valide formatos antes de enviar
- Remova campos não documentados
- Consulte a documentação do endpoint

#### 401 Unauthorized
Não autenticado ou credenciais inválidas.

**Exemplos:**

```json
{
  "type": "https://httpstatuses.com/401",
  "title": "Unauthorized",
  "status": 401,
  "detail": "Unauthorized"
}
```

```json
{
  "type": "https://httpstatuses.com/401",
  "title": "Unauthorized",
  "status": 401,
  "detail": "Invalid or expired access token"
}
```

```json
{
  "type": "https://httpstatuses.com/401",
  "title": "Unauthorized",
  "status": 401,
  "detail": "Unauthorized to access this seller [j1k2l3]",
  "trace_id": "j1k2l3"
}
```

**Causas comuns:**
- Token OAuth expirado (após 1 hora)
- Token inválido ou ausente
- `certSerialNumber` não corresponde ao `marketplace_id`
- Tentando acessar seller de outro marketplace
- Certificado mTLS inválido

**Solução:**
- Obtenha um novo token OAuth 2.0
- Verifique o `marketplace_id` na URL
- Confirme que o seller pertence ao seu marketplace
- Valide seu certificado mTLS

#### 403 Forbidden
Autenticado, mas sem permissão para acessar o recurso.

```json
{
  "type": "https://httpstatuses.com/403",
  "title": "Forbidden",
  "status": 403,
  "detail": "Insufficient permissions"
}
```

**Causas comuns:**
- Tentando acessar recursos de outro marketplace
- Permissões insuficientes no client_id

**Solução:**
- Verifique se o recurso pertence ao seu marketplace
- Entre em contato com suporte para revisar permissões

#### 404 Not Found
Recurso não encontrado.

```json
{
  "type": "https://httpstatuses.com/404",
  "title": "Not Found",
  "status": 404,
  "detail": "Transaction not found"
}
```

```json
{
  "type": "https://httpstatuses.com/404",
  "title": "Not Found",
  "status": 404,
  "detail": "Seller not found"
}
```

**Causas comuns:**
- ID incorreto na URL
- Recurso foi deletado
- Recurso pertence a outro marketplace

**Solução:**
- Verifique o ID do recurso
- Confirme que o recurso existe
- Verifique se está usando o marketplace_id correto

#### 429 Too Many Requests
Rate limit excedido.

```json
{
  "type": "https://httpstatuses.com/429",
  "title": "Too Many Requests",
  "status": 429,
  "detail": "Rate limit exceeded. Try again in 60 seconds",
  "retry_after": 60
}
```

**Solução:**
- Adicione cache de respostas
- Otimize número de requisições
- Entre em contato com o suporte

### 5xx - Erros do Servidor

#### 500 Internal Server Error
Erro interno do servidor.

```json
{
  "type": "https://httpstatuses.com/500",
  "title": "Internal Server Error",
  "status": 500,
  "detail": "Unknown error occurred [m4n5o6]",
  "trace_id": "m4n5o6"
}
```

**Causas comuns:**
- Erro inesperado no servidor
- Falha na comunicação com gateway de pagamento

**Solução:**
- Tente novamente após alguns segundos
- Se persistir, reporte usando o `trace_id`

#### 501 Not Implemented
Funcionalidade ainda não implementada.

```json
{
  "type": "https://httpstatuses.com/501",
  "title": "Not Implemented",
  "status": 501,
  "detail": "MPOS transaction flow not yet implemented [p7q8r9]",
  "trace_id": "p7q8r9",
  "validated_data": {...}
}
```

**Causas comuns:**
- Usando `payment_locale` que ainda não está ativo (PINPAD, MPOS, Tap to Pay, Link Payment)

**Solução:**
- Use `payment_locale`: "api" (ou omita o campo)
- Aguarde disponibilidade da funcionalidade
- Entre em contato com suporte para roadmap

#### 502 Bad Gateway
Erro na comunicação com serviço externo.

```json
{
  "type": "https://httpstatuses.com/502",
  "title": "Bad Gateway",
  "status": 502,
  "detail": "Error communicating with payment gateway",
  "trace_id": "s1t2u3"
}
```

**Causas comuns:**
- Gateway de pagamento indisponível
- Timeout na comunicação
- Erro de rede

**Solução:**
- Tente novamente após alguns segundos
- Implemente retry com backoff exponencial
- Se persistir, reporte o `trace_id`

#### 503 Service Unavailable
Serviço temporariamente indisponível.

```json
{
  "type": "https://httpstatuses.com/503",
  "title": "Service Unavailable",
  "status": 503,
  "detail": "Service under maintenance",
  "retry_after": 300
}
```

**Causas comuns:**
- Manutenção programada
- Alta carga no sistema

**Solução:**
- Aguarde e tente novamente
- Respeite o header `Retry-After`

## Erros Específicos de Negócio

### Validação de Transação

```json
{
  "type": "https://httpstatuses.com/400",
  "title": "Bad Request",
  "status": 400,
  "detail": "Field amount must be a positive number",
  "trace_id": "v4w5x6"
}
```

```json
{
  "type": "https://httpstatuses.com/400",
  "title": "Bad Request",
  "status": 400,
  "detail": "Invalid payment_type. Allowed values: credit, debit, boleto, bolepix, pix",
  "trace_id": "y7z8a9"
}
```

```json
{
  "type": "https://httpstatuses.com/400",
  "title": "Bad Request",
  "status": 400,
  "detail": "Field installment_plan.number_installments must be between 1 and 12",
  "trace_id": "b1c2d3"
}
```

### Erro do Gateway de Pagamento

Quando o gateway retorna erro, a API repassa a mensagem:

```json
{
  "type": "https://httpstatuses.com/400",
  "title": "Bad Request",
  "status": 400,
  "detail": "Cartão com restrição",
  "category": "card_error",
  "trace_id": "e4f5g6"
}
```

## Tratamento de Erros - Melhores Práticas

### 1. Sempre Verifique o Status HTTP

```javascript
const response = await fetch(url, options);

if (!response.ok) {
  const error = await response.json();
  console.error(`Error ${error.status}: ${error.detail}`);
  console.error(`Trace ID: ${error.trace_id}`);
  throw new Error(error.detail);
}
```

### 2. Log do Trace ID

```javascript
catch (error) {
  console.error('API Error:', {
    status: error.status,
    detail: error.detail,
    trace_id: error.trace_id,  // IMPORTANTE para suporte
    timestamp: new Date().toISOString()
  });
  
  // Enviar para sistema de monitoramento
  sendToMonitoring({
    type: 'api_error',
    trace_id: error.trace_id,
    details: error
  });
}
```

### 3. Mensagens Amigáveis para Usuário

```javascript
function getUserFriendlyMessage(error) {
  switch (error.status) {
    case 400:
      return 'Verifique os dados informados e tente novamente.';
    case 401:
      return 'Sessão expirada. Faça login novamente.';
    case 404:
      return 'Recurso não encontrado.';
    case 429:
      return 'Muitas requisições. Aguarde um momento.';
    case 500:
    case 502:
    case 503:
      return 'Erro temporário. Tente novamente em instantes.';
    default:
      return 'Ocorreu um erro inesperado.';
  }
}
```

## Trace ID para Suporte

Sempre que reportar um problema ao suporte, inclua:

```
Trace ID: a1b2c3
Timestamp: 2025-12-21T10:30:00Z
Endpoint: POST /v1/marketplaces/abc.../transactions
Status: 500
Mensagem: Unknown error occurred
```

O `trace_id` permite que nossa equipe rastreie o que aconteceu de forma facilitada.

## Próximos Passos

- [Começar a Testar](./testando.md)
- [Criar Primeira Transação](../transacoes/criar/api/cartao.md)
- [Ver Exemplos Completos](../vendedores/listar.md)
