# Como configurar o Pixel do Facebook

Você pode integrar o Pixel do Facebook aos seus produtos para rastrear as vendas e otimizar as campanhas de anúncio.

No menu principal clique em Produtos e depois selecione o produto que deseja adicionar o pixel.

Em seguida, clique na aba Configurações:

![](/assets/ads/01_fb_menu_config.png)

Desça a tela até a opção de Pixels de conversão.

Preencha com o seu Pixel ID:

![](/assets/ads/02_fb_menu_opcoes.png)

{% hint style="warning" %}
**Importante:** O nosso sistema permite adicionar até 50 Pixels do Facebook em cada produto.
{% endhint %}

Para cada Pixel, você deve selecionar um domínio onde ele será disparado.

Por padrão adicionamos o domínio da plataforma da GOPag, mas recomendamos que você coloque o domínio do seu website ou página de vendas, o mesmo que você verifica no Facebook. 

## Perguntas frequentes

### Como funciona a verificação de domínio?

Os pixels são disparados no seu domínio, que você pode conectar à plataforma da GOPag. Esse deve ser o mesmo domínio verificado no Facebook.

<!-- LEMBRAR DE PREENCHER O LINK DE ATALHO-->

Temos um tutorial explicando o passo a passo, [clique aqui]().

### Como adicionar uma porcentagem de conversão personalizada para pix ou boletos gerados?

É possível selecionar a porcentagem do valor do pix ou do boleto gerado para que ele tenha a conversão personalizada, gerando o evento de "purchase".

Essa ferramenta irá otimizar as suas campanhas, visto que auxilia a estimar uma porcentagem de conversão e qual valor de venda deve ser enviado ao Facebook.

Para ativar essa função é necessário habilitar o evento Purchase ao gerar um pix ou boleto e adicionar a porcentagem desejada.

Veja o exemplo abaixo em que selecionamos a porcentagem de 50% do valor do boleto para enviar o evento de "purchase".

![](/assets/ads/03_fb_menu_switch.png)

### Quais eventos a pltaforma GOPag envia para o Pixel?

- InitiateCheckout (quando alguém visita o checkout)
- Purchase (compra aprovada no cartão ou PIX)
- Boleto (boleto gerado)

Em compras aprovadas no cartão, disparamos juntamente com o evento de "Purchase", o evento de "credit_card".

Em compras aprovadas no pix, disparamos juntamente com o evento de "Purchase", o evento de "pix".

Por padrão, não disparamos o evento "purchase" para boleto gerado, mas você pode habilitar essa opção se preferir.

{% hint style="warning" %}
**Importante:** Compra recusada no cartão de crédito não gera evento de "purchase".
{% endhint %}

### Onde encontrar meu Pixel do Facebook?

Acesse o Facebook Business Manager https://business.facebook.com e no menu à esquerda clique em Mais ferramentas, depois em Gerenciador de eventos

![](/assets/ads/04_fb_menu_gerenciador.png)

