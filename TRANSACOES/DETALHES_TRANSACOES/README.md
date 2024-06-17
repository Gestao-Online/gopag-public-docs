# Detalhes transações

Agora no detalhamento de cada transação existem alguns pontos importantes a tratar, de início a tela que você verá será parecida com esta:

![](/assets/prints/transacoes_menu_detalhe_transacao.png)

<br>

Já no primeiro card podemos ver o **`Status`** do pagamento, que pode ter as opções: 

- Pago
- Cancelado
- Pendente
- Falha
- Novo
- Pré-autorizado
- Revertido
- Reembolsado
- Disputa
- Charged back

Enquanto que no segundo card podemos ver a data e hora da transação. Fechamos com o terceiro card exibindo qual foi a forma de pagamento utilizada pelo cliente:

![](/assets/prints/transacoes_menu_detalhe_transacao_card_123.png)

<br>

No quarto card pode ver os **`Dados do vendedor`** e descrição do link de cobrança gerado, enquanto no quinto card podemos ver os **`Dados do pagador`**, sendo eles, nome, quantidade de parcelas que esse cliente utilizou (Dependendo do tanto que você determinou ao criar o link de cobrança), o número do cartão que foi utilizado e validade do mesmo. 

Além do código de autorização e código de NSU que usamos para identificar cada transação de cartão, seja crédito ou débito. 😉

![](/assets/prints/transacoes_menu_detalhe_transacao_card_4_e_5.png)

<br>

Logo no sexto card você pode visualizar os **`Detalhes da transação`**, tais como valores, a taxa de venda que foi aplicada, e o valor líquido que vai como saldo para você, assim como o ID da transação de referência.

![](/assets/prints/transacoes_menu_detalhe_transacao_card_6.png)

Na parte inicial da tela de detalhes da transação você pode ver um botão chamado **`Estornar pagamento`**, esse botāo faz com o que os pagamentos em cartāo de crédito ou PIX seja cancelado (os valores serāo devolvidos):

{% hint style="warning" %}
**Importante:** Este recurso **`Estornar pagamento`** não está disponível para boleto bancário.
{% endhint %}

![](/assets/prints/transacoes_menu_detalhe_transacao_btn_estorno.png)

![](/assets/prints/transacoes_menu_detalhe_transacao_btn_estorno_1.png)

<br>

Em caso de uso do boleto, existem algumas diferenças nas informações de pagamento, no caso abaixo temos a linha digitável do boleto, a descrição com o dia/horário que foi efetuado o pagamento e também o ID do pagamento:

![](/assets/prints/transacoes_menu_detalhe_transacao_boleto.png)

<br>

E na parte inicial da tela de detalhes da **`transação de boleto`** você pode ver um botão chamado **`Cancelar`**, ele irá te ajudar no momento que for necessário encerrar a emissão de um boleto, ou alguma transação para fazer estorno ao cliente.

O boleto será cancelado (nāo poderá mais ser pago pelo cliente) e também saíra do DDA(Débito Direto Autorizado) com prazo de 1 dia útil.

![](/assets/prints/transacoes_menu_detalhe_transacao_boleto_2.png)