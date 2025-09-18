# Overview



* API baseada em RESTful
* Requisições no padrão JSON
* Endpoints organizados por contexto de negócio
* Versionamento implícito via ambiente (stage, prod)
* Tratamento padrão de erros HTTP
* Integração com OAuth2 (/oauth) para autenticação

### 🚦 Nomenclaturas

| Seller/Vendedor | Nome da empresa ou pessoa física com conta na GOPAG            |
| --------------- | -------------------------------------------------------------- |
| Buyer/Comprador | Nome do cliente final (pessoa que está pagando para um seller) |
| Business Unit   | Nome da unidade interna na API, atrelada a um seller           |
| User            | Usuário atrelado a uma unidade/seller                          |

## 🔐 Ambientes, autenticação e Segurança

### 🌐 URLs Base <a href="#docs-internal-guid-6c253dd3-7fff-fefa-704e-dc90cac6b74f" id="docs-internal-guid-6c253dd3-7fff-fefa-704e-dc90cac6b74f"></a>

* Ambiente de produção: https://app.gopag.com.br
* Ambiente de homologação/stage : https://app-stage.gopag.com.br

### 🔐 Token de API

Obtenha o token de autenticação pelo portal GoPag.

Todas as requisições à API devem incluir o seguinte cabeçalho HTTP:

```
Authorization: Bearer <seu_token_aqui>
```

Substitua `<seu_token_aqui>` pelo token obtido no portal. Esse token é obrigatório para autenticação e autorização de chamadas à API, tanto em ambiente de **produção** quanto em **homologação (stage)**.

Certifique-se também de enviar as requisições com o cabeçalho:

```
Content-Type: application/json
```

### 📦 Códigos de retorno

## Error Constants and Messages

| Constant                                           | Message (pt\_BR)                                                | Message (en\_US)                                              |
| -------------------------------------------------- | --------------------------------------------------------------- | ------------------------------------------------------------- |
| BUYER\_CREATED\_SUCCESS                            | Comprador criado com sucesso                                    | Buyer created with success                                    |
| BUYER\_UPDATED\_SUCCESS\_1                         | Comprador atualizado com sucesso                                | Buyer updated successfully                                    |
| BUYER\_UPDATED\_SUCCESS\_2                         | Comprador já estava atualizado                                  | Buyer already up to date                                      |
| BUYER\_FAIL\_ADD\_OR\_UPDATE\_1                    | Falha ao adicionar ou atualizar comprador \[1]                  | Fail to add or update buyer \[1]                              |
| BUYER\_FAIL\_ADD\_OR\_UPDATE\_3                    | Falha ao adicionar ou atualizar comprador \[3]                  | Fail to add or update buyer \[3]                              |
| BUYER\_FETCH\_SUCCESS                              | Dados da comprador obtidos com sucesso                          | Buyer data fetched successfully                               |
| BUYER\_FORBIDDEN                                   | Acesso negado ao comprador                                      | Buyer access denied                                           |
| BUYER\_TAXPAYER\_ID\_NOT\_FOUND                    | CPF/CNPJ não encontrado                                         | TaxpayerId not found                                          |
| INVOICY\_CREATED                                   | Cobrança criada com sucesso                                     | Invoicy created successfully                                  |
| INVOICY\_DELETED                                   | Cobrança deletada com sucesso                                   | Invoicy deleted successfully                                  |
| INVOICY\_UPDATED                                   | Cobrança atualizada com sucesso                                 | Invoicy updated successfully                                  |
| INVOICY\_FAIL\_ADD\_BUYER                          | Falha ao adicionar comprador                                    | Fail to pay invoicy, failed to add buyer                      |
| INVOICY\_FAIL\_ADD\_BUYER\_MISMATCH                | Falha ao adicionar comprador: dados inconsistentes              | Fail to pay invoicy, failed to add buyer due to data mismatch |
| INVOICY\_FAIL\_NOTIFY                              | Falha ao enviar notificação                                     | Failed to send notification                                   |
| INVOICY\_FAIL\_DELETE                              | Falha ao deletar cobrança                                       | Failed to delete invoicy                                      |
| INVOICY\_FAIL\_TOKENIZATION                        | Falha ao tokenizar cartão de crédito                            | Failed to tokenize credit card                                |
| INVOICY\_FAIL\_UNKNOWN                             | Erro desconhecido na cobrança                                   | Unknown error while on invoicy                                |
| INVOICY\_FAILED\_FROM\_SOURCE                      | Falha ao gerar cobrança a partir da origem                      | Failed to generate invoicy from source                        |
| INVOICY\_FETCH\_ERROR                              | Falha ao obter os dados da cobrança                             | Failed to fetch invoicy data                                  |
| INVOICY\_FETCH\_SUCCESS                            | Dados da cobrança obtidos com sucesso                           | Invoicy data fetched successfully                             |
| INVOICY\_FORBIDDEN                                 | Acesso negado à cobrança                                        | Invoicy access denied                                         |
| INVOICY\_GENERATED\_FROM\_SOURCE                   | Cobrança gerada a partir da origem com sucesso                  | Invoicy successfully generated from source                    |
| INVOICY\_INSTALLMENT\_PLAN\_INVALID                | Plano de parcelamento inválido para este tipo de pagamento      | Invalid installment plan for selected payment type            |
| INVOICY\_INVALID\_FIELD\_3DS                       | Campo obrigatório do 3D Secure está ausente ou inválido         | Required 3D Secure field is missing or invalid                |
| INVOICY\_INVALID\_FIELD\_CREDIT\_CARD              | Campo obrigatório do cartão de crédito está ausente ou inválido | Required credit card field is missing or invalid              |
| INVOICY\_INVALID\_TYPE                             | Tipo de cobrança inválido                                       | Invalid invoicy type                                          |
| INVOICY\_NOT\_FOUND                                | Cobrança não encontrada                                         | Invoicy not found                                             |
| INVOICY\_NOTHING\_TO\_RUN\_NEXT\_DATE              | Nada a processar: data da próxima execução não chegou           | Nothing to run: next execution date has not arrived           |
| INVOICY\_NOTHING\_TO\_RUN\_NEXT\_RETRY\_DATE       | Nada a processar: data da próxima tentativa execução não chegou | Nothing to run: next retry execution date has not arrived     |
| INVOICY\_NOTHING\_TO\_RUN\_PAYMENT\_TYPE           | Nada a processar: tipo de pagamento vazio                       | Nothing to run: empty payment type                            |
| INVOICY\_NOTHING\_TO\_RUN\_STATUS                  | Nada a processar: status inválido para execução                 | Nothing to run: status not eligible for execution             |
| INVOICY\_PAYMENT\_TYPE\_MISMATCH                   | Tipo de pagamento diferente do selecionado anteriormente        | Selected payment type does not match previously selected      |
| INVOICY\_PAYMENT\_TYPE\_UNAVAILABLE                | Tipo de pagamento indisponível                                  | Selected payment type is unavailable                          |
| INVOICY\_RECURRENCE\_PROCESSED                     | Cobrança de recorrência processada com sucesso                  | Recurrence invoicy processed successfully                     |
| INVOICY\_RECURRENCE\_PROCESSED\_WITH\_ERRORS       | Cobrança de recorrência processada com erros                    | Recurrence invoicy processed with errors                      |
| INVOICY\_RECURRENCE\_UPDATED                       | Cobrança de recorrência atualizada com sucesso                  | Recurrence updated successfully                               |
| INVOICY\_STATUS\_INVALID                           | Status atual não permite pagamento                              | Current status does not allow payment                         |
| INVOICY\_SUCCESS\_NOTIFY                           | Notificação enviada com sucesso                                 | Notification sent successfully                                |
| INVOICY\_EXCEED\_ATTEMPTS\_TOKENIZED\_CREDIT\_CARD | Cartão de crédito tokenizado na cobrança bloqueado              | Tokenized credit card blocked for invoicy                     |
| INVOICY\_EXCEED\_ATTEMPTS\_RECURRENCE              | Cobrança recorrente excedeu as tentativas                       | Invoicy recurrence exceeded attempts                          |
| INVOICY\_TOKENIZE\_INVALID\_CUSTOMER               | Cliente retornado na tokenização é inválido                     | Returned customer on tokenization is invalid                  |
| INVOICY\_TOKENIZE\_INVALID\_RESPONSE               | Resposta inválida ao tentar tokenizar o cartão                  | Invalid response while tokenizing card                        |
