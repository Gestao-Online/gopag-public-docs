# Requisitos de Segurança

A segurança é fundamental para o processamento de pagamentos. A GoPag API implementa múltiplas camadas de proteção para garantir a integridade e confidencialidade dos dados.

## 🔐 Camadas de Segurança

### 1. Bearer Token (OAuth 2.0)

Token de autenticação fornecido via Portal GoPag ou enviado pela GoPag (Entre em contato para solicitar)

> **📋 OBTENÇÃO DO TOKEN**
>
> O Bearer Token pode ser obtido de duas formas:
> - **Portal GoPag**: Acesse Configurações > API > Exportar Token
> - **Canal de Suporte/Consultor**: Token enviado via e-mail ou canal seguro
>
> 📧 Contato: suporte@gopag.com.br

#### Características
- **Tipo**: Bearer
- **Uso**: Header `Authorization` em todas as requisições
- **Validade**: Configurável (geralmente longa duração)

#### Usando o Token

Inclua o token no header `Authorization`:

```http
GET /v1/marketplaces/abc123.../sellers HTTP/1.1
Host: api.gopag.com.br
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGc...
```

### 2. mTLS (Mutual TLS) - Certificado ICP-Brasil

Autenticação bidirecional através de certificados digitais.

> **📋 CREDENCIAMENTO NECESSÁRIO**
>
> Para integrar com a GoPag API, você precisará de um **certificado digital ICP-Brasil** (e-CPF ou e-CNPJ).
> 
> Entre em contato com nossa equipe de suporte para iniciar o processo de credenciamento.
>
> 📧 Email: suporte@gopag.com.br

#### Como Funciona
1. **Cliente se autentica**: Apresenta certificado digital ICP-Brasil
2. **Servidor valida**: Verifica a autenticidade do certificado
3. **Servidor se autentica**: Apresenta seu próprio certificado
4. **Cliente valida**: Verifica a autenticidade do servidor
5. **Conexão estabelecida**: Canal criptografado bidirecional

#### Certificado do Cliente
- Formato: Certificado Digital ICP-Brasil (e-CPF ou e-CNPJ)
- Padrão: X.509 PEM
- Algoritmo: RSA 2048 bits ou superior
- Emissão: Fornecido pela equipe GoPag após credenciamento
- Serial Number: Deve corresponder ao `marketplace_id`

**⚠️ IMPORTANTE**: O `certSerialNumber` do certificado **DEVE** ser igual ao `marketplace_id` usado na URL. Esta é uma verificação crítica de segurança.

### 3. Isolamento por Marketplace

Cada marketplace possui acesso isolado aos seus próprios recursos:

- ✅ **Permitido**: Acessar apenas sellers do seu marketplace
- ❌ **Bloqueado**: Acessar sellers de outros marketplaces
- ✅ **Validado**: `certSerialNumber === marketplace_id` em todas as requisições

#### Exemplo de Isolamento

```http
GET /v1/marketplaces/marketplace-A/sellers HTTP/1.1
Authorization: Bearer token-marketplace-A

✅ SUCESSO - certSerialNumber do certificado = marketplace-A
```

```http
GET /v1/marketplaces/marketplace-B/sellers HTTP/1.1
Authorization: Bearer token-marketplace-A

❌ ERRO 401 - certSerialNumber não corresponde ao marketplace_id
```

## 🔑 Gestão de Credenciais

### Armazenamento Seguro

**Nunca armazene credenciais em:**
- ❌ Código-fonte
- ❌ Repositórios Git
- ❌ Arquivos de configuração versionados
- ❌ Logs de aplicação
- ❌ Banco de dados sem criptografia

**Armazene credenciais em:**
- ✅ Variáveis de ambiente
- ✅ Cofres de segredos (AWS Secrets Manager, Azure Key Vault, etc.)
- ✅ Sistemas de gestão de configuração seguros
- ✅ HSM (Hardware Security Module) para ambientes críticos

### Rotação de Credenciais

Recomendamos rotacionar suas credenciais regularmente:

- **Client Secret**: A cada 90 dias
- **Certificados mTLS**: Anualmente (antes do vencimento)
- **Tokens de Acesso**: Gerados dinamicamente (1 hora de validade)

### Certificado mTLS - Boas Práticas

```bash
# Armazene o certificado e chave em arquivos separados
/etc/ssl/certs/gopag-client.crt  # Certificado público
/etc/ssl/private/gopag-client.key # Chave privada

# Defina permissões restritivas
chmod 644 /etc/ssl/certs/gopag-client.crt
chmod 600 /etc/ssl/private/gopag-client.key

# Apenas o usuário da aplicação deve ter acesso
chown app-user:app-group /etc/ssl/private/gopag-client.key
```

## 🛡️ Proteção de Dados Sensíveis

### Dados de Cartão

**NUNCA armazene:**
- ❌ Número completo do cartão (`card_number`)
- ❌ Código de segurança CVV (`security_code`)
- ❌ Trilha magnética
- ❌ PIN do cartão

**Você PODE armazenar:**
- ✅ Token do cartão (ID retornado pela API)
- ✅ Primeiros 4 dígitos (`first4_digits`)
- ✅ Últimos 4 dígitos (`last4_digits`)
- ✅ Data de validade (se necessário para UX)
- ✅ Nome do portador

#### Tokenização

Use a funcionalidade de tokenização para armazenar cartões de forma segura:

```json
{
  "source": {
    "card": {
      "card_number": "4111111111111111",
      "holder_name": "João Silva",
      "expiration_month": "12",
      "expiration_year": "2026",
      "security_code": "123"
    },
    "usage": "reusable"
  }
}
```

**Resposta:**
```json
{
  "id": "abc123def456...",
  "first4_digits": "4111",
  "last4_digits": "1111",
  "card_brand": "visa"
}
```

Nas próximas transações, use apenas o `id`:

```json
{
  "source": {
    "card": {
      "id": "abc123def456..."
    }
  }
}
```

### Dados Pessoais (LGPD/GDPR)

Para conformidade com LGPD e GDPR:

#### Minimize a Coleta
- Colete apenas dados necessários para a transação
- Não armazene dados pessoais desnecessariamente
- Use campos opcionais quando apropriado

#### Anonimização
- Remova ou mascare CPF/CNPJ em logs
- Use hash para identificação interna quando possível
- Implemente políticas de retenção de dados

#### Direitos do Titular
- Permita exclusão de dados (DELETE endpoints)
- Forneça acesso aos dados armazenados (GET endpoints)
- Mantenha registro de consentimento

## 🚨 Detecção de Ameaças

### Monitoramento de Segurança

A GoPag monitora continuamente:

- **Tentativas de acesso não autorizado**
- **Padrões anormais de requisições**
- **Uso de certificados revogados**
- **Requisições de IPs suspeitos**
- **Tentativas de força bruta**

### Trace ID

Todas as requisições geram um `trace_id` único para rastreamento:

```json
{
  "trace_id": "a1b2c3"
}
```

Use este ID para:
- Debugar problemas
- Reportar incidentes de segurança
- Auditar operações

### Logs de Auditoria

Mantemos logs detalhados de todas as operações:

```json
{
  "event": "TRANSACTION_CREATE_START",
  "timestamp": "2025-12-21T10:30:00Z",
  "trace_id": "a1b2c3",
  "marketplace_id": "abc123...",
  "user_id": "user123",
  "ip_address": "203.0.113.42",
  "action": "create_transaction"
}
```

## 🔒 Checklist de Segurança

Antes de ir para produção, verifique:

- [ ] Certificado mTLS configurado e testado
- [ ] Client Secret armazenado em cofre seguro
- [ ] Validação de `marketplace_id` implementada
- [ ] Tokens OAuth renovados automaticamente
- [ ] Dados sensíveis não armazenados localmente
- [ ] Logs não expõem credenciais ou dados de cartão
- [ ] HTTPS obrigatório em todas as comunicações
- [ ] Rate limiting implementado no cliente
- [ ] Tratamento de erros não expõe informações sensíveis
- [ ] Política de rotação de credenciais definida
- [ ] Monitoramento de segurança configurado
- [ ] Plano de resposta a incidentes documentado

## 📞 Reportando Problemas de Segurança

Se você identificar uma vulnerabilidade de segurança:

1. **NÃO** divulgue publicamente
2. Entre em contato imediatamente com suporte@gopag.com.br
3. Forneça detalhes da vulnerabilidade e steps para reproduzir
4. Aguarde nossa resposta (SLA: 24 horas)

Agradecemos pesquisadores de segurança responsáveis!

## Próximos Passos

- [Configurar Autenticação](./autenticacao.md)
- [Testar a API](./testando.md)
- [Entender Códigos de Erro](./codigos-erro.md)
