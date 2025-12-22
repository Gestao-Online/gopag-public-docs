# Criar cobrança única

Cria uma cobrança única do tipo `detached`.\
\
Esse tipo de cobrança pode ser configurado para aceitar diferentes meios de pagamento, como **cartão de crédito (à vista ou parcelado)**, **Pix**, entre outros.

Os dados do cliente podem ou não ser informados no momento da criação da cobrança (Via API). Caso não sejam, o próprio cliente deverá preenchê-los diretamente ao acessar o **link de pagamento**.

Como retorno, você receberá a **URL da cobrança**, que poderá ser compartilhada ou integrada ao seu sistema, permitindo que o cliente seja direcionado para realizar o pagamento de forma simples e segura.

### ✅ Campos obrigatórios mínimos

* `amount`: Valor da cobrança
* `type`: Deve ser `"detached"`

### 👤 Dados do Cliente (`customer`)

Os dados do cliente (como nome, e-mail, CPF/CNPJ) **são opcionais** no momento da criação da cobrança.

* Se **forem informados**, o cliente será identificado automaticamente no link de pagamento.
* Se **não forem informados**, o cliente será solicitado a preencher seus dados antes de efetuar o pagamento.

### 💳 Formas de Pagamento e parcelamento

O campo `paymentType` controla quais formas de pagamento estarão disponíveis para o usuário no momento do pagamento pelo link

* `credit`: Opção de pagamento com cartão de crédito
* `pix`: Opção de pagamento por QRCode ou PIX copia e cola (EVP)
* `boleto`: Opção de pagamento por Boleto

Já  o campo `installmentPlan` controla as opções de parcelamento (na modalidade de cartão de crédito)

### 📧 Envio de Notificação (`sendNotification`)

O campo `sendNotification` controla se o sistema deve ou não **enviar um e-mail automático para o cliente**, contendo o link da cobrança.

* `true`: um e-mail será enviado para o cliente&#x20;
* `false`: nenhuma notificação será enviada

## 🔗 Endpoint de API

`POST /invoicy`

### 🔐 Headers obrigatórios

```
Accept: application/json  
Content-Type: application/json  
Authorization: Bearer <seu_token>
```

Exemplo de chamada mínima:

```
{
   "amount": 123.45,
   "type": "detached",
   "description": "Descrição opcional"
}
```

### 📘 Exemplo 1: Criar cobrança **com dados do cliente**

Indicado quando você já possui as informações do pagador (nome, e-mail, CPF, etc.).

```
{
  "description": "Cobrança com cliente",
  "amount": 150.00,
  "type": "detached",
  "currency": "BRL",
  "customerName": "João da Silva",
  "customerEmail": "joao@email.com",
  "customerPhoneNumber": "(11) 91234-5678",
  "customerTaxpayerId": "123.456.789-09",
  "addressPostalCode": "01001-000",
  "addressCity": "São Paulo",
  "addressState": "SP",
  "addressCountryCode": "BR",
  "addressNeighborhood": "Centro",
  "addressLine1": "Rua Exemplo",
  "addressLine2": "123",
  "paymentType": {
    "credit": true,
    "pix": true
  },
  "installmentPlan": {
    "1x": true,
    "2x": true,
    "3x": true
  },
  "sendNotification": true
}
```

### 📘 Exemplo 2: Criar cobrança **sem dados do cliente**

Ideal para gerar cobranças onde o cliente irá preencher seus próprios dados posteriormente na hora de realizar o pagamento no link.

```
{
  "description": "Cobrança sem cliente",
  "amount": 200.00,
  "type": "detached",
  "currency": "BRL",
  "paymentType": {
    "credit": true,
    "pix": true
  },
  "installmentPlan": {
    "1x": true,
    "2x": true
  },
  "sendNotification": false
}

```

## 🧾 Campos específicos para cobrança via **boleto**

Esses campos permitem configurar vencimento, juros, multa e a data limite para pagamento. São úteis para garantir que o boleto siga regras de cobrança financeira bem definidas.

#### 🔹 `boletoDueDate`

* **Descrição:** Data de vencimento do boleto.
* **Formato:** `YYYY-MM-DD` (ex: `"2025-08-15"`)
* **Obrigatório para:** cobranças com `boleto` ou `bolepix` ativado.

#### `boletoLimitDate`

* **Descrição:** Data limite para aceitar o pagamento após o vencimento.
* **Se omitido:** o boleto vence na data do `boletoDueDate` e não poderá mais ser pago.
* **Usado para:** permitir que o boleto seja pago mesmo após vencido, até uma data máxima definida.

#### `boletoLateFeeMode`

* **Descrição:** Define como será aplicada a **multa por atraso**.
* **Opções:**
  * `"FIXED"`: valor fixo em reais (ex: R$ 5,00)
  * `"PERCENTAGE"`: percentual sobre o valor original (ex: 2%)

> ⚠️ Esse campo deve ser usado junto com `boletoLateFeeAmount` ou `boletoLateFeePercentage`, dependendo do modo escolhido.

#### `boletoLateFeeAmount`

* **Descrição:** Valor fixo da multa por atraso.
* **Exemplo:** `2.50` (representa R$ 2,50)
* **Usado apenas se:** `boletoLateFeeMode` for `"FIXED"`

#### `boletoLateFeePercentage`

* **Descrição:** Percentual da multa por atraso.
* **Exemplo:** `2.00` (representa 2% do valor original)
* **Usado apenas se:** `boletoLateFeeMode` for `"PERCENTAGE"`

#### `boletoInterestMode`

* **Descrição:** Define como serão cobrados os **juros por dia de atraso**.
* **Opções:**
  * `"DAILY_AMOUNT"`: valor fixo por dia de atraso (ex: R$ 0,50/dia)
  * `"DAILY_PERCENTAGE"`: percentual diário sobre o valor da cobrança (ex: 0,1% ao dia)

> ⚠️ Esse campo deve ser combinado com `boletoInterestAmount` ou `boletoInterestPercentage`, dependendo do modo escolhido.

#### `boletoInterestAmount`

* **Descrição:** Valor fixo de juros por dia de atraso.
* **Exemplo:** `0.33` (R$ 0,33 por dia de atraso)
* **Usado apenas se:** `boletoInterestMode` for `"DAILY_AMOUNT"`

#### `boletoInterestPercentage`

* **Descrição:** Percentual de juros por dia de atraso.
* **Exemplo:** `0.10` (0,10% ao dia)
* **Usado apenas se:** `boletoInterestMode` for `"DAILY_PERCENTAGE"`

### 📘 Exemplo 3: Criar cobrança via **boleto**

Inclui parâmetros específicos como data de vencimento, juros e multa.

```
{
  "description": "Cobrança via boleto",
  "amount": 300.00,
  "type": "detached",
  "currency": "BRL",
  "customerName": "Maria Oliveira",
  "customerEmail": "maria@email.com",
  "customerTaxpayerId": "987.654.321-00",
  "addressPostalCode": "30140-110",
  "addressCity": "Belo Horizonte",
  "addressState": "MG",
  "addressCountryCode": "BR",
  "addressNeighborhood": "Savassi",
  "addressLine1": "Av. Exemplo",
  "addressLine2": "456",
  "paymentType": {
    "boleto": true,
    "bolepix": true
  },
  "boletoDueDate": "2025-08-15",
  "boletoLateFeeMode": "FIXED",
  "boletoLateFeeAmount": 2.50,
  "boletoInterestMode": "DAILY_AMOUNT",
  "boletoInterestAmount": 0.33,
  "boletoLimitDate": "2025-08-30",
  "sendNotification": true
}

```
