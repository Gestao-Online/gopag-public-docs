# Criar Transação com Cartão de Crédito/Débito

## Visão Geral

Este guia explica como processar pagamentos não presenciais com cartão de crédito e débito na GoPag utilizando **tokenização segura no navegador**, em conformidade com **PCI DSS**.

## Por Que Usar Tokenização?

A tokenização de cartões é uma prática essencial de segurança que:

- ✅ **Protege dados sensíveis**: Os dados do cartão nunca passam pelo seu servidor
- ✅ **Reduz riscos de compliance**: Você não armazena ou processa informações de cartão
- ✅ **Simplifica auditorias PCI DSS**: Menor escopo de conformidade
- ✅ **Aumenta a segurança**: Tokenização acontece em ambiente certificado PCI DSS (Zoop)
- ✅ **Melhora a confiança**: Seus clientes têm dados protegidos desde o início

## Como Funciona o Fluxo

```
┌─────────────┐      ┌──────────────┐      ┌─────────────┐      ┌──────────────┐
│  Navegador  │─────▶│  SDK GoPag   │─────▶│  Zoop API   │─────▶│   Token      │
│  (Cliente)  │      │  (JavaScript)│      │ (PCI DSS)   │      │  Temporário  │
└─────────────┘      └──────────────┘      └─────────────┘      └──────────────┘
                                                                         │
                                                                         ▼
┌─────────────┐      ┌──────────────┐      ┌─────────────┐      ┌──────────────┐
│  GoPag API  │◀─────│  Seu Backend │◀─────│   Envio de  │◀─────│   Token      │
│ (Processar) │      │   (Servidor) │      │    Token    │      │   Seguro     │
└─────────────┘      └──────────────┘      └─────────────┘      └──────────────┘
```

**O fluxo em etapas:**

1. **Cliente** insere dados do cartão no navegador
2. **SDK GoPag** (JavaScript) captura e tokeniza os dados diretamente com a **Zoop** (ambiente PCI DSS)
3. **Token temporário** é gerado e retornado ao navegador
4. **Seu backend** recebe apenas o token (não os dados do cartão)
5. **GoPag API** processa a transação usando o token seguro em comunicação com o provedor Zoop

**🔒 Importante:** Os dados sensíveis do cartão **NUNCA** passam pelo seu servidor, sendo enviados diretamente para o provedor.

## Pré-requisitos

### 1. Instalar o SDK JavaScript

Você precisa do **GoPag JS SDK** para tokenizar cartões no navegador:

#### Via NPM

```bash
npm install js-gopag-sdk
```

#### Via CDN

```html
<script src="https://cdn.jsdelivr.net/npm/js-gopag-sdk/dist/gopag-sdk.umd.min.js"></script>
```

### 2. Credenciais Necessárias

- **Publishable Key da Zoop** (`zpk_...`) 
- **Marketplace ID**
- **Token de API GoPag** (para seu backend)

📚 [Documentação completa do SDK](https://github.com/gopag-oficial/js-gopag-sdk)

---

## Passo 1: Tokenizar o Cartão no Navegador

### Inicializar o SDK

```javascript
// Inicializar o SDK GoPag no frontend
const gopag = new GoPagSDK({
  publishableKey: 'zsk_test_xxxxxxxxxxxxx',
  marketplaceId: 'seu_marketplace_id',
  environment: 'sandbox', // ou 'production'
  enable3DS: true // Habilita coleta de dados 3D Secure
});
```

### Coletar e Tokenizar Dados do Cartão

```javascript
// Dados do cartão coletados do formulário
const cardData = {
  holder_name: 'João da Silva',
  card_number: '4111111111111111',
  expiration_month: '12',
  expiration_year: '2025',
  security_code: '123'
};

// Dados de faturamento (opcional, para 3D Secure)
const options = {
  billing: {
    address: 'Rua Exemplo, 123',
    city: 'São Paulo',
    postal_code: '01234-567',
    state: 'SP',
    country: 'BR',
    email_address: 'cliente@example.com',
    phone_number: '11987654321'
  }
};

// Tokenizar o cartão
gopag.tokenize(cardData, options)
  .then(result => {
    if (result.success) {
      console.log('Token gerado:', result.card.token);
      console.log('Dados 3DS:', result.three_d_secure);
      
      // Enviar token para seu backend
      enviarTokenParaBackend(result);
    } else {
      console.error('Erro na tokenização:', result.error);
    }
  });
```

### Resposta do SDK

```json
{
  "success": true,
  "processingTime": 1234,
  "card": {
    "token": "a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6",
    "id": "9f8e7d6c5b4a3210fedcba9876543210",
    "first4": "4111",
    "last4": "1111",
    "brand": "visa",
    "expiration_month": "12",
    "expiration_year": "2025",
    "holder_name": "João da Silva",
    "is_valid": true,
    "fingerprint": "1a2b3c4d5e6f..."
  },
  "three_d_secure": {
    "user_agent": "Mozilla/5.0...",
    "challenge_type": "DATA_ONLY",
    "device": {
      "color_depth": 24,
      "type": "BROWSER",
      "java_enabled": false,
      "language": "pt-BR",
      "screen_height": 1080,
      "screen_width": 1920,
      "time_zone_offset": -3
    },
    "billing": {
      "address": "Rua Exemplo, 123",
      "city": "São Paulo",
      "postal_code": "01234567",
      "state": "sp",
      "country": "br",
      "email_address": "cliente@example.com",
      "phone_number": 11987654321
    }
  },
  "recaptcha": {
    "token": "ABC123DEF456...",
    "action": "tokenize_card"
  }
}
```

**🔒 Segurança:** O token retornado é **temporário** e pode ser usado apenas uma vez para criar a transação.

---

## Passo 2: Enviar Token para o Backend

No seu frontend, envie o token (e dados 3DS) para o seu servidor usando os dados retornados pela lib/sdk:

```javascript
async function enviarTokenParaBackend(tokenData) {
  const response = await fetch('/api/processar-pagamento', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      pciTokenSdk: btoa(JSON.stringify(tokenData)), // Token codificado
      amount: 10000, // R$ 100,00 em centavos
      description: 'Pagamento de produto XYZ',
      seller_id: 'seller_id_aqui',
      customer_id: 'customer_id_aqui' // opcional
    })
  });
  
  const result = await response.json();
  console.log('Transação criada:', result);
}
```

---

## Passo 3: Criar Transação na API GoPag

No seu **backend**, use o token recebido para criar a transação:

### Endpoint

```
POST /v1/marketplaces/{marketplace_id}/transactions
```

### Requisição com Token SDK

```http
POST /v1/marketplaces/abc123.../transactions HTTP/1.1
Host: api.gopag.com.br
Authorization: Bearer {seu_access_token}
Content-Type: application/json
```

```json
{
  "payment_type": "credit",
  "installment_plan": {
    "number_installments": 1
  },
  "pciTokenSdk": "eyJzdWNjZXNzIjp0cnVlLCJwcm9jZXNzaW5nVGltZSI6MTM3MiwiY2FyZCI6eyJ0b2tlbiI6ImExYjJjM2Q0ZTVmNmc3aDhpOWowazFsMm0zbjRvNXA2IiwiaWQiOiI5ZjhlN2Q2YzViNGEzMjEwZmVkY2JhOTg3NjU0MzIxMCIsImZpcnN0NCI6IjQxMTEiLCJicmFuZCI6IlZpc2EiLCJleHBpcmF0aW9uX21vbnRoIjoiMTIiLCJleHBpcmF0aW9uX3llYXIiOiIyMDI1IiwiaG9sZGVyX25hbWUiOiJKT0FPIERBIFNJTFZBIiwiaXNfdmFsaWQiOnRydWUsImlzX3ZlcmlmaWVkIjpmYWxzZSwiZmluZ2VycHJpbnQiOiIxYTJiM2M0ZDVlNmY3ZzhoOWkwajFrMmwzbTRuNW82cDdxOHI5czB0MXUydjN3NHg1eTZ6N2E4YjljMGQxZTJmM2c0In0sInJlY2FwdGNoYSI6eyJ0b2tlbiI6IkFCQzEyM0RFRjQ1NkdISTc4OUpLTDAxMk1OTzM0NVBRUjY3OFNUVTkwMVZXWDIzNFlaQTU2N0JDRDg5MEVGRzEyM0hJSjQ1NktMTTc4OU5PUDAxMlFSUzM0NVRVVjY3OFdYWTkwMVpBQjIzNENERTU2N0ZHSDg5MElKSzEyM0xNTjQ1Nk9QUTc4OVJTVDAxMlVWVzM0NVhZWjY3OEFCQzkwMURFRjIzNEdISTU2N0pLTDg5ME1OTzEyM1BRUjQ1NlNUVTc4OVZXWDAxMllaQTM0NUJDRDY3OEVGRzkwMUhJSjIzNEtMTTU2N05PUDg5MFFSUzEyM1RVVjQ1NldYWTc4OVpBQjAxMkNERTM0NUZHSDY3OElKSzkwMUxNTjIzNE9QUTU2N1JTVDg5MFVWVzEyM1hZWjQ1NkFCQzc4OURFRjAxMkdISTM0NUpLTDY3OE1OTzkwMVBRUjIzNFNUVTU2N1ZXWDg5MFlaQTEyM0JDRDQ1NkVGRzc4OUhJSjAxMktMTTM0NU5PUDY3OFFSUzkwMVRVVjIzNFdYWTU2N1pBQjg5MENERTEyM0ZHSDQ1NklKSzc4OUxNTjAxMk9QUTM0NVJTVDY3OFVWVzkwMVhZWjIzNEFCQzU2N0RFRjg5MEdISTEyM0pLTDQ1Nk1OTzc4OVBRUjAxMlNUVTM0NVZXWDY3OFlaQTkwMUJDRDIzNEVGRzU2N0hJSjg5MEtMTTEyM05PUDQ1NlFSUzc4OVRVVjAxMldYWTM0NVpBQjY3OENERTkwMUZHSDIzNElKSzU2N0xNTjg5ME9QUTEyM1JTVDQ1NlVWVzc4OVhZWjAxMiIsImFjdGlvbiI6InRva2VuaXplX2NhcmQifSwidGhyZWVfZF9zZWN1cmUiOnsidXNlcl9hZ2VudCI6Ik1vemlsbGEvNS4wIChXaW5kb3dzIE5UIDEwLjA7IFdpbjY0OyB4NjQpIEFwcGxlV2ViS2l0LzUzNy4zNiAoS0hUTUwsIGxpa2UgR2Vja28pIENocm9tZS85MS4wLjQ0NzIuMTI0IFNhZmFyaS81MzcuMzYiLCJjaGFsbGVuZ2VfdHlwZSI6IkRBVEFfT05MWSIsImRldmljZSI6eyJjb2xvcl9kZXB0aCI6MjQsInR5cGUiOiJCUk9XU0VSIiwiamF2YV9lbmFibGVkIjpmYWxzZSwibGFuZ3VhZ2UiOiJwdC1CUiIsInNjcmVlbl9oZWlnaHQiOjEwODAsInNjcmVlbl93aWR0aCI6MTkyMCwidGltZV96b25lX29mZnNldCI6M30sImJpbGxpbmciOnsiYWRkcmVzcyI6IlJ1YSBFeGVtcGxvIDEyMyIsImNpdHkiOiJTw6NvIFBhdWxvIiwicG9zdGFsX2NvZGUiOiIwMTIzNDU2NyIsInN0YXRlIjoic3AiLCJjb3VudHJ5IjoiYnIiLCJlbWFpbF9hZGRyZXNzIjoiZXhlbXBsb0Bnb3BhZy5jb20uYnIiLCJwaG9uZV9udW1iZXIiOjExOTg3NjU0MzIxfX19",
  "on_behalf_of": "54f5445fd4544b454654fdf46545465g",
  "three_d_secure": {
    "on_failure": "continue",
    "ip_address": "ip do cliente que seu backend precisa repassar"
  },
  "customer": "customer_id_opcional",
  "source": {
    "amount": 10000,
    "currency": "BRL"
  },
  "description": "Compra do produto ou serviço x/y/z",
  "capture": true,
  "reference_id": "pedido-12345"
}
```

### Parâmetros Principais

| Campo | Tipo | Obrigatório | Descrição |
|-------|------|-------------|-----------|
| `payment_type` | string | Sim | `credit` ou `debit` |
| `pciTokenSdk` | string | Sim | Token codificado em base64 retornado pelo SDK |
| `on_behalf_of` | string | Sim | ID do vendedor (seller_id) |
| `source.amount` | integer | Sim | Valor em centavos (ex: 10000 = R$ 100,00) |
| `source.currency` | string | Não | Moeda (padrão: `BRL`) |
| `description` | string | Não | Descrição da transação |
| `capture` | boolean | Não | `true` para captura imediata (padrão: `true`) |
| `reference_id` | string | Não | Seu identificador único |
| `customer` | string | Não | ID do cliente (para associar o pagamento) |

### Parâmetros de 3D Secure

| Campo | Tipo | Descrição |
|-------|------|-----------|
| `three_d_secure.on_failure` | string | `continue` ou `halt` - O que fazer se 3DS falhar |
| `three_d_secure.ip_address` | string | IP do cliente |

### Parâmetros de Parcelamento (apenas crédito)

```json
{
  "installment_plan": {
    "number_installments": 3
  }
}
```

| Campo | Tipo | Descrição |
|-------|------|-----------|
| `installment_plan.number_installments` | integer | Número de parcelas (1-12) |

---

## Resposta da API

### Sucesso (201 Created)

```json
{
  "resource": "transaction",
  "id": "abc123def456789...",
  "status": "succeeded",
  "amount": 10000,
  "original_amount": 10000,
  "currency": "BRL",
  "description": "Compra de produto X",
  "payment_type": "credit",
  "transaction_number": "123456",
  "gateway_authorizer": "cielo",
  "on_behalf_of": "54f5445fd4544b454654fdf46545465g",
  "confirmed": true,
  "captured": true,
  "refunded": false,
  "voided": false,
  "reference_id": "pedido-12345",
  "payment_method": {
    "id": "card-id...",
    "resource": "card",
    "card_brand": "visa",
    "first4_digits": "4111",
    "last4_digits": "1111",
    "expiration_month": "12",
    "expiration_year": "2025",
    "holder_name": "João da Silva"
  },
  "payment_authorization": {
    "authorizer_id": "00",
    "authorization_code": "123456",
    "authorization_nsu": "789012"
  },
  "installments": 1,
  "fees": 250,
  "created_at": "2025-12-21T10:30:00Z",
  "updated_at": "2025-12-21T10:30:15Z"
}
```

### Status Possíveis

| Status | Descrição |
|--------|-----------|
| `succeeded` | Transação aprovada e capturada |
| `pre_authorized` | Autorizada, aguardando captura |
| `failed` | Transação recusada |
| `pending` | Processando |
| `canceled` | Cancelada |

---

## Segurança e Boas Práticas

### ✅ SEMPRE Faça

- ✅ **Use o SDK no frontend** para tokenizar cartões
- ✅ **Use HTTPS** em produção
- ✅ **Valide dados no backend** antes de enviar para a API
- ✅ **Armazene apenas o `card_id`** se precisar reutilizar o cartão
- ✅ **Implemente rate limiting** para prevenir fraudes
- ✅ **Use 3D Secure** para transações de maior valor
- ✅ **Monitore transações** em tempo real

### ❌ NUNCA Faça

- ❌ **NUNCA armazene `card_number`** no seu banco de dados
- ❌ **NUNCA armazene `security_code` (CVV)**
- ❌ **NUNCA envie dados brutos de cartão** para seu backend
- ❌ **NUNCA logue dados sensíveis** de cartão
- ❌ **NUNCA compartilhe credenciais** em código-fonte
- ❌ **NUNCA desabilite validações** de segurança

---

## Conformidade PCI DSS

### Por Que Este Fluxo é Seguro?

1. **Dados sensíveis não tocam seu servidor**
   - O SDK tokeniza diretamente com a Zoop (certificada PCI DSS Level 1)
   - Seu servidor recebe apenas tokens temporários
   
2. **Redução de escopo PCI**
   - Você não processa, armazena ou transmite dados de cartão
   - Auditorias PCI são simplificadas
   
3. **Proteção contra vazamentos**
   - Mesmo se seu servidor for comprometido, não há dados de cartão
   
4. **Tokens descartáveis**
   - Cada token é válido apenas para uma transação
   - Não pode ser reutilizado se interceptado

### Benefícios de Compliance

| Sem Tokenização | Com Tokenização (SDK) |
|-----------------|----------------------|
| ❌ Alto risco de exposição | ✅ Risco mínimo |
| ❌ Auditoria PCI complexa | ✅ Auditoria simplificada |
| ❌ Responsabilidade total | ✅ Responsabilidade compartilhada |
| ❌ Custos elevados | ✅ Custos reduzidos |
| ❌ Possível multa por vazamento | ✅ Proteção contra vazamentos |

---

## Compatibilidade e Integração

O SDK GoPag é compatível com:

### 📱 Frameworks e Bibliotecas

- **Vanilla JavaScript** (puro)
- **React** / Next.js
- **Vue.js** / Nuxt.js
- **Angular**
- **jQuery**
- **Svelte**

### 🌐 Ambientes

- **Navegadores modernos** (Chrome, Firefox, Safari, Edge etc)
- **Dispositivos móveis** (iOS Safari, Chrome Mobile)

---

---

## Exemplo Completo de Integração

### Frontend (HTML + JavaScript)

```html
<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <title>Checkout Seguro - GoPag</title>
  <script src="https://cdn.jsdelivr.net/npm/js-gopag-sdk/dist/gopag-sdk.umd.min.js"></script>
</head>
<body>
  <form id="payment-form">
    <h2>Pagamento Seguro</h2>
    
    <input type="text" id="holder-name" placeholder="Nome no cartão" required>
    <input type="text" id="card-number" placeholder="Número do cartão" required>
    <input type="text" id="expiry-month" placeholder="Mês (MM)" required>
    <input type="text" id="expiry-year" placeholder="Ano (YYYY)" required>
    <input type="text" id="cvv" placeholder="CVV" required>
    
    <h3>Dados de Faturamento</h3>
    <input type="text" id="address" placeholder="Endereço" required>
    <input type="text" id="city" placeholder="Cidade" required>
    <input type="text" id="postal-code" placeholder="CEP" required>
    <input type="text" id="state" placeholder="Estado (SP)" required>
    <input type="email" id="email" placeholder="E-mail" required>
    <input type="tel" id="phone" placeholder="Telefone" required>
    
    <button type="submit">Pagar R$ 100,00</button>
  </form>

  <script>
    // Inicializar SDK
    const gopag = new GoPagSDK({
      publishableKey: 'zsk_test_xxxxxxxxxxxxx',
      marketplaceId: 'seu_marketplace_id',
      environment: 'sandbox',
      enable3DS: true
    });

    document.getElementById('payment-form').addEventListener('submit', async (e) => {
      e.preventDefault();

      // Coletar dados do cartão
      const cardData = {
        holder_name: document.getElementById('holder-name').value,
        card_number: document.getElementById('card-number').value,
        expiration_month: document.getElementById('expiry-month').value,
        expiration_year: document.getElementById('expiry-year').value,
        security_code: document.getElementById('cvv').value
      };

      // Coletar dados de faturamento
      const options = {
        billing: {
          address: document.getElementById('address').value,
          city: document.getElementById('city').value,
          postal_code: document.getElementById('postal-code').value,
          state: document.getElementById('state').value,
          email_address: document.getElementById('email').value,
          phone_number: document.getElementById('phone').value
        }
      };

      try {
        // Tokenizar no navegador
        const result = await gopag.tokenize(cardData, options);

        if (result.success) {
          // Enviar token para o backend
          const response = await fetch('/api/processar-pagamento', {
            method: 'POST',
            headers: { 'Content-Type': 'application/json' },
            body: JSON.stringify({
              pciTokenSdk: btoa(JSON.stringify(result)),
              amount: 10000,
              seller_id: 'seller_id_aqui'
            })
          });

          const transaction = await response.json();
          
          if (transaction.status === 'succeeded') {
            alert('Pagamento aprovado!');
          } else {
            alert('Pagamento recusado: ' + transaction.status);
          }
        } else {
          alert('Erro na tokenização: ' + result.error);
        }
      } catch (error) {
        alert('Erro ao processar pagamento');
        console.error(error);
      }
    });
  </script>
</body>
</html>
```

### Backend (Node.js + Express)

```javascript
const express = require('express');
const axios = require('axios');
const app = express();

app.use(express.json());

app.post('/api/processar-pagamento', async (req, res) => {
  const { pciTokenSdk, amount, seller_id } = req.body;

  try {
    // Chamar API GoPag
    const response = await axios.post(
      'https://api.gopag.com.br/v1/marketplaces/seu_marketplace_id/transactions',
      {
        payment_type: 'credit',
        pciTokenSdk: pciTokenSdk,
        on_behalf_of: seller_id,
        source: {
          amount: amount,
          currency: 'BRL'
        },
        installment_plan: {
          number_installments: 1
        },
        three_d_secure: {
          on_failure: 'continue',
          ip_address: req.ip
        },
        description: 'Pagamento via checkout',
        capture: true,
        reference_id: `order-${Date.now()}`
      },
      {
        headers: {
          'Authorization': `Bearer ${process.env.GOPAG_API_TOKEN}`,
          'Content-Type': 'application/json'
        }
      }
    );

    res.json(response.data);
  } catch (error) {
    console.error('Erro ao processar pagamento:', error.response?.data || error.message);
    res.status(500).json({ error: 'Erro ao processar pagamento' });
  }
});

app.listen(3000, () => {
  console.log('Servidor rodando na porta 3000');
});
```

### Backend (PHP)

```php
<?php
// processar-pagamento.php

header('Content-Type: application/json');

$data = json_decode(file_get_contents('php://input'), true);

$pciTokenSdk = $data['pciTokenSdk'];
$amount = $data['amount'];
$seller_id = $data['seller_id'];

$payload = [
    'payment_type' => 'credit',
    'pciTokenSdk' => $pciTokenSdk,
    'on_behalf_of' => $seller_id,
    'source' => [
        'amount' => $amount,
        'currency' => 'BRL'
    ],
    'installment_plan' => [
        'number_installments' => 1
    ],
    'three_d_secure' => [
        'on_failure' => 'continue',
        'ip_address' => $_SERVER['REMOTE_ADDR']
    ],
    'description' => 'Pagamento via checkout',
    'capture' => true,
    'reference_id' => 'order-' . time()
];

$ch = curl_init('https://api.gopag.com.br/v1/marketplaces/seu_marketplace_id/transactions');
curl_setopt($ch, CURLOPT_POST, true);
curl_setopt($ch, CURLOPT_POSTFIELDS, json_encode($payload));
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_HTTPHEADER, [
    'Authorization: Bearer ' . getenv('GOPAG_API_TOKEN'),
    'Content-Type: application/json'
]);

$response = curl_exec($ch);
$httpCode = curl_getinfo($ch, CURLINFO_HTTP_CODE);
curl_close($ch);

http_response_code($httpCode);
echo $response;
?>
```

## Captura Manual (Pré-Autorização)

Para autorizar o pagamento sem capturar imediatamente:

### 1. Criar transação sem captura

```json
{
  "payment_type": "credit",
  "pciTokenSdk": "token_aqui...",
  "capture": false,
  ...
}
```

**Resposta:**
```json
{
  "status": "pre_authorized",
  "captured": false
}
```

### 2. Capturar posteriormente

```
POST /v1/marketplaces/{marketplace_id}/transactions/{transaction_id}/capture
```

```json
{
  "amount": 10000,
  "on_behalf_of": "seller_id_aqui"
}
```

📚 [Ver documentação completa de Captura](../../capturar.md)

---

## Split de Pagamento

Para dividir o pagamento entre múltiplos vendedores:

```json
{
  "payment_type": "credit",
  "pciTokenSdk": "token_aqui...",
  "on_behalf_of": "seller_principal",
  "source": {
    "amount": 10000,
    "currency": "BRL"
  },
  "split_rules": [
    {
      "liable": true,
      "charge_processing_fee": false,
      "recipient": "seller_id_1",
      "percentage": 70
    },
    {
      "liable": false,
      "charge_processing_fee": true,
      "recipient": "seller_id_2",
      "percentage": 30
    }
  ],
  "description": "Pagamento dividido",
  "capture": true
}
```

---

## Tratamento de Erros

### Erros Comuns

#### Token inválido ou expirado

```json
{
  "status": 400,
  "detail": "Invalid or expired pciTokenSdk",
  "trace_id": "abc123"
}
```

**Solução:** Gere um novo token no frontend.

#### Seller não autorizado

```json
{
  "status": 401,
  "detail": "Unauthorized to access this seller [seller_id]",
  "trace_id": "def456"
}
```

**Solução:** Verifique se o `on_behalf_of` está correto e autorizado.

#### Transação recusada

```json
{
  "status": "failed",
  "gateway_message": "Cartão com restrição",
  "gateway_response_code": "05"
}
```

**Solução:** Informe ao cliente para usar outro cartão ou entrar em contato com o banco.

#### Parcelamento inválido

```json
{
  "status": 400,
  "detail": "Field installment_plan.number_installments must be between 1 and 12",
  "trace_id": "ghi789"
}
```

**Solução:** Ajuste o número de parcelas.

---

## Testes em Sandbox

### Cartões de Teste

Use estes cartões no ambiente sandbox:

| Bandeira | Número | CVV | Validade | Resultado |
|----------|--------|-----|----------|-----------|
| Visa | `4111111111111111` | `123` | `12/2025` | ✅ Aprovado |
| Mastercard | `5555555555554444` | `123` | `12/2025` | ✅ Aprovado |
| Elo | `6362970000457013` | `123` | `12/2025` | ✅ Aprovado |
| Visa | `4000000000000010` | `123` | `12/2025` | ❌ Recusado |

### Ambiente de Teste

```javascript
const gopag = new GoPagSDK({
  publishableKey: 'zsk_test_xxxxxxxxxxxxx',
  marketplaceId: 'marketplace_test_id',
  environment: 'sandbox' // <-- Usar sandbox para testes
});
```

**Base URL Sandbox:** `https://api-sandbox.gopag.com.br`

---

## FAQ - Perguntas Frequentes

### 1. Por que devo usar o SDK em vez de enviar os dados diretamente?

**R:** O SDK garante que dados sensíveis do cartão **nunca passem pelo seu servidor**, mantendo você em conformidade com PCI DSS e reduzindo riscos de segurança.

### 2. O token pode ser reutilizado?

**R:** Não. Tokens gerados pelo SDK são **temporários e descartáveis**, válidos apenas para uma transação.

### 3. Preciso certificar meu servidor PCI DSS?

**R:** Não. Como os dados do cartão são tokenizados no navegador, seu servidor **não processa dados sensíveis**, reduzindo drasticamente o escopo PCI.

### 4. E se o cliente desabilitar JavaScript?

**R:** A tokenização requer JavaScript habilitado. Considere exibir uma mensagem informando que JavaScript é necessário para pagamentos seguros.

### 5. Posso salvar o cartão para uso futuro?

**R:** Sim. Use o `card_id` retornado na resposta da transação para associar ao cliente. **NUNCA** armazene `card_number` ou `security_code`.

### 6. O SDK funciona em aplicativos móveis?

**R:** O SDK é projetado para navegadores web. Para apps nativos, use WebView ou consulte a documentação de SDKs móveis da GoPag.

### 7. Como testar 3D Secure em sandbox?

**R:** Use os cartões de teste fornecidos. O ambiente sandbox simula fluxos 3DS sem exigir autenticação real.

### 8. Há limite de tentativas de tokenização?

**R:** Sim. Por segurança, há rate limiting. Em caso de múltiplas falhas, aguarde alguns segundos antes de tentar novamente.

---

---

## Recursos Adicionais

### 📚 Documentação

- [SDK GoPag JavaScript](https://github.com/gopag-oficial/js-gopag-sdk) - Repositório oficial
- [Documentação API GoPag](https://api.gopag.com.br/docs) - Referência completa
- [Guia de 3D Secure](../../../seguranca/3ds.md) - Autenticação adicional
- [Webhooks](../../../webhooks/introducao.md) - Notificações de transação

### 🔐 Segurança

- [Boas Práticas PCI DSS](../../../seguranca/pci-dss.md)
- [Gestão de Credenciais](../../../seguranca/credenciais.md)
- [Prevenção de Fraudes](../../../seguranca/fraudes.md)

### 💡 Exemplos Práticos

- [Exemplo React + TypeScript](https://github.com/gopag-oficial/examples/tree/main/react-checkout)
- [Exemplo Vue.js](https://github.com/gopag-oficial/examples/tree/main/vue-checkout)
- [Exemplo Node.js Backend](https://github.com/gopag-oficial/examples/tree/main/nodejs-backend)

---

## Próximos Passos

Agora que você sabe como criar transações com cartão de forma segura:

- 📊 [Listar Transações](../../listar.md) - Consultar histórico de pagamentos
- 💰 [Capturar Transação](../../capturar.md) - Confirmar pré-autorizações
- 🔄 [Estornar Transação](../../estornar.md) - Processar devoluções
---

## Suporte

Precisa de ajuda? Entre em contato:

- 📧 **E-mail:** suporte@gopag.com.br
- 📖 **Documentação:** [docs.gopag.com.br](https://docs.gopag.com.br)
- 💬 **Chat:** Disponível no painel GoPag
- 🐛 **Reportar bugs:** [GitHub Issues](https://github.com/gopag-oficial/js-gopag-sdk/issues)

---

## Privacidade e Proteção de Dados

A tokenização de cartões é processada pela **Zoop (iFoodPago)**, parceiro certificado PCI DSS Level 1 da GoPag. Os dados sensíveis dos cartões são protegidos de acordo com os mais altos padrões de segurança da indústria.

**🔒 Importante:** Os dados completos do cartão (número, CVV) **não devem ser armazenados nem transacionados** por intermediários, incluindo:
- ❌ Seu backend (servidor da aplicação)
- ❌ Gateway GoPag
- ✅ Apenas a Zoop (ambiente certificado PCI DSS) processa os dados sensíveis

Somente **tokens temporários e seguros** são transmitidos entre os sistemas, garantindo máxima proteção e privacidade dos dados dos seus clientes.

Para mais informações sobre como seus dados são tratados, consulte:

📄 **Política de Privacidade da Zoop (iFoodPago):**  
[https://www.zoop.com.br/politicas/declaracao-de-privacidade-pago](https://www.zoop.com.br/politicas/declaracao-de-privacidade-pago)


