# Visão Geral

## O que é a GoPag API?

A GoPag API é uma solução completa para processamento de pagamentos que permite a integração de múltiplos métodos de pagamento em sua aplicação. Com suporte a cartões de crédito/débito, boletos, PIX e dispositivos físicos (MPOS, PINPAD, Tap to Pay), nossa API oferece flexibilidade e segurança para seu negócio.

## Características Principais

### 🔐 Segurança de Nível Empresarial
- Autenticação OAuth 2.0 com Bearer Token
- Certificados ICP-Brasil (mTLS) para autenticação bidirecional
- Isolamento completo entre marketplaces
- Criptografia end-to-end

### 💳 Múltiplos Métodos de Pagamento

#### Pagamentos Online (API)
- **Cartão de Crédito**: Até 12x parcelamento
- **Cartão de Débito**: Pagamento à vista
- **Boleto**: Com ou sem PIX (Bolepix)
- **PIX**: Pagamento instantâneo
- **Link de Pagamento**: Interface pronta para compartilhar com clientes

#### Dispositivos Físicos
- **Tap to Pay**: Pagamento por aproximação via smartphone
- **MPOS**: Maquininha mobile conectada via Bluetooth
- **PINPAD**: Terminal fixo com digitação de senha

### 📊 Recursos Avançados
- **Tokenização de Cartões**: Armazene cartões de forma segura para reuso
- **Split de Pagamento**: Divida automaticamente entre múltiplos recebedores
- **3D Secure**: Autenticação adicional para maior segurança
- **Captura Manual**: Autorize agora, capture depois
- **Metadata Customizado**: Adicione informações personalizadas às transações

## Arquitetura da API

A GoPag API segue o padrão REST com respostas no formato HAL+JSON (Hypertext Application Language).

### Base URLs

```
Homologação: https://api-stage.gopag.com.br
Produção: https://api.gopag.com.br
```

### Versionamento

A API utiliza versionamento na URL:

```
/v1/marketplaces/{marketplace_id}/...
```

Sempre recomendamos usar a versão mais recente para ter acesso aos recursos mais novos.

## Estrutura de Requisição

Todas as requisições devem incluir:

1. **Authorization Header**: Bearer Token (obtido via Portal GoPag ou consultor)
2. **Certificado mTLS**: Certificado ICP-Brasil para autenticação bidirecional
3. **Content-Type**: `application/json` para requisições POST/PATCH
4. **Accept**: `application/json` para respostas

### Exemplo de Headers

```http
POST /v1/marketplaces/abc123.../transactions HTTP/1.1
Host: api.gopag.com.br
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGc...
Content-Type: application/json
Accept: application/json
```

## Estrutura de Resposta

As respostas seguem o formato HAL+JSON com campos padronizados:

```json
{
  "resource": "transaction",
  "uri": "/v1/marketplaces/.../transactions/123",
  "id": "abc123def456...",
  "status": "succeeded",
  "amount": 10000,
  "currency": "BRL",
  "created_at": "2025-12-21T10:30:00Z",
  "updated_at": "2025-12-21T10:30:15Z"
}
```

### Coleções (Listas)

```json
{
  "resource": "list",
  "uri": "/v1/marketplaces/.../sellers",
  "items": [...],
  "total": 150,
  "limit": 20,
  "offset": 0,
  "has_more": true
}
```

## Valores Monetários

Todos os valores monetários são representados em **centavos** (inteiros):

- R$ 100,00 = `10000`
- R$ 1,50 = `150`
- R$ 0,99 = `99`

## Formatos de Data

Todas as datas seguem o padrão ISO 8601:

```
2025-12-21T10:30:00Z
```

## Próximos Passos

1. [Configure sua autenticação](./autenticacao.md)
2. [Entenda os requisitos de segurança](./requisitos-seguranca.md)
3. [Teste sua primeira requisição](./testando.md)
4. Comece a integrar:
   - [Cadastre vendedores](../vendedores/listar.md)
   - [Cadastre compradores](../compradores/criar.md)
   - [Crie sua primeira transação](../transacoes/criar/api/cartao.md)
