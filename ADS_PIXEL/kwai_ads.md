# ⚪ Pixel do Kwai

### Como configurar o Pixel do Kwai

A plataforma da GOPag integra com o pixel do Kwai. O primeiro passo é coletar o código do seu pixel Kwai, para depois configurar na GOPag.

### Coletando o seu Pixel no Kwai

Dentro do **Ads Manager** do Kwai, clique no menu **Assets**, depois em **Pixel**.

![](/assets/ads/48_kwai_pixel.png)

Então, clique na opção **Create New Pixel**.

![](/assets/ads/49_kwai_pixel_create.png)

Na tela seguinte, insira o nome do seu pixel, selecione a opção **Developer Mode** e clique em **Create**. Isso é necessário para o Pixel funcionar com a plataforma da GOPag.

![](/assets/ads/50_kwai_pixel_create.png)

Clique em **Copy** para copiar o código do seu pixel.

![](/assets/ads/51_kwai_pixel_copy_id.png)

Ao final do código, localize o seu **Pixel ID** como no exemplo:

![](/assets/ads/52_kwai_pixel_copy_id.png)

Seguindo o exemplo acima, o ID extraído seria esse: **410984069679040162**

{% hint style="warning" %}
**Importante:** Para que a integração funcione, você precisa gerar o seu próprio ID, este é apenas um exemplo.
{% endhint %}

Pronto, agora já tem o seu Pixel ID do Kwai!

### Colocando o Pixel ID do Kwai na plataforma da GOPag

Agora dentro da plataforma da GOPag, acesse **Produtos -> Selecione o seu produto -> Configurações**, clique na opção do Kwai e cole o Pixel ID lá.

![](/assets/ads/53_kwai_pixel_colar.png)

Após isso, você pode salvar o seu produto.

### Como adicionar uma porcentagem de conversão personalizada para pix e boletos gerados?

É possível selecionar a porcentagem do valor do pix ou boleto para que ele tenha a conversão personalizada, gerando o evento de **Purchase**.

Essa ferramenta irá otimizar as campanhas, visto que auxilia a estimar uma porcentagem de conversão e qual valor de venda deve ser enviado ao Kwai Ads.

Veja o exemplo abaixo em que selecionamos a porcentagem de 50% do valor do boleto para enviar o evento de **Purchase**.

Para ativar essa função é necessário habilitar abaixo do Pixel ID.

