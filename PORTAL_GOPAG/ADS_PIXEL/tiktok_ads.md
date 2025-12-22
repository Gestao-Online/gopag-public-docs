# ⚫ Pixel do TikTok

### Como configurar o pixel do TikTok

A plataforma da GOPag integra com o pixel do TikTok. O primeiro passo é coletar o código do seu pixel TikTok, para depois configurar na GOPag.

### Coletando o seu Pixel no TikTok

No **Ads Manager do TikTok**, clique no menu **Ferramentas**, depois em **Evento**.

![](../../.gitbook/assets/39_tiktok_pixel.png)

Então, clique na opção **Gerenciar** em **Eventos para web**.

![](../../.gitbook/assets/40_tiktok_pixel_eventos.png)

Depois em **Configurar eventos web -> Configuração manual** e, então, adicione um nome para o seu pixel.

![](../../.gitbook/assets/41_tiktok_pixel_eventos_nome.png) ![](../../.gitbook/assets/42_tiktok_pixel_eventos_nome.png)

Após isso, será necessário pular a etapa de criação do funil, visto que a plataforma da GOPag irá captar os eventos de maneira automática. Depois, selecione a opção "Código personalizado" e prossiga. Isso é necessário para o pixel funcionar com a GOPag.

![](../../.gitbook/assets/43_tiktok_pixel_cod_personalizado.png)

Clique em **Exibir instruções** e copie o código gerado.

![](../../.gitbook/assets/44_tiktok_pixel_cod_personalizado.png)

Exemplo de código:

![](../../.gitbook/assets/45_tiktok_pixel_cod_exemplo.png)

É necessário agora extrair o pixel ID. Seguindo o exemplo acima, o ID extraído seria esse: **CAUALHRC77U7KHVMKB4G**.

{% hint style="warning" %}
**Importante:** Lembrando que o exemplo acima é fictício, é necessário criar o próprio pixel ID.
{% endhint %}

Pronto, esse é o seu pixel ID do TikTok.

### Colocando o Pixel ID do TikTok na plataforma da GOPag

Agora na plataforma da GOPag, acesse **Produtos -> Selecione o seu produto -> Configurações**, clique na opção do TikTok e cole o Pixel ID lá.

![](../../.gitbook/assets/46_tiktok_pixel_config.png)

Após isso, é possível salvar o produto.

### Como adicionar uma porcentagem de conversão personalizada para pix e boletos gerados?

É possível selecionar a porcentagem do valor do pix ou boleto para que ele tenha a conversão personalizada, gerando o evento de **Complete Payment**.

Essa ferramenta irá otimizar as campanhas, visto que auxilia a estimar uma porcentagem de conversão e qual valor de venda deve ser enviado ao TikTok Ads.

Veja o exemplo abaixo em que selecionamos a porcentagem de 50% do valor do boleto para enviar o evento de **Complete Payment**.

Para ativar essa função, é necessário habilitar abaixo do Pixel ID.

![](../../.gitbook/assets/47_tiktok_pixel_complete_payment.png)

### Quais eventos a plataforma da GOPag envia para o Pixel?

* **Initiate Checkout** (Quando alguém visita o checkout).
* **Complete payment** (Compra aprovada no cartão ou PIX).

Por padrão, não disparamos o evento **Complete payment** para boleto gerado, mas é possível habilitar essa opção.

\
<br>
