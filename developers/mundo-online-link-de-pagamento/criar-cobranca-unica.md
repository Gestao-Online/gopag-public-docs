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

#### 🔹 `boletoInterestPercentage`

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
