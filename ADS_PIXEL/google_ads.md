# 🟢 Pixel do Google

### Como adicionar o pixel de conversão do Google Ads

Você pode adicionar até 5 pixels ou eventos de conversão por produto.

O primeiro passo é gerar ou encontrar o seu Pixel do Google Ads

### Encontrando o ID do Pixel Google Ads e Label de conversão

O ID do Pixel Google Ads é único para a sua conta inteira do Google, ou seja, ele não muda.

O Label de conversão muda quando você cria conversões diferentes no Google Ads.

Você pode encontrar o seu Pixel Google Ads em Metas -> Resumo -> Nova ação de conversão

![](/assets/ads/19_gg_menu_pixel.png)

![](/assets/ads/20_gg_menu_pixel_resumo.png)

Será preciso selecionar a opção "Criar ações de conversão manualmente usando código" e, após isso, salvar e continuar para a próxima etapa.

![](/assets/ads/21_gg_menu_pixel_add_conversao.png)

Depois de concluir a criação do pixel, clique na opção **ver snippet do evento**:

![](/assets/ads/22_gg_menu_pixel_snippet.png)

O ID do Pixel Google ads e o Label de conversão são separados por uma barra ` / `.

![](/assets/ads/23_gg_menu_id.png)

Os dados da foto acima seriam:

**ID do Pixel Google Ads:** AW-11468698711
**Label de conversão:** FW1mCOf_i4cZENfo2dwq

### Adicionando o pixel do Google Ads no seu produto

Você pode adicionar até 5 pixels ou eventos de conversão diferentes por produto

Basta ir em **Editar produto** -> aba **Configurações**

Então desça até a seção de Pixels de conversão (é no mesmo local onde coloca o Pixel do Facebook). E clique na aba Google Ads.

![](/assets/ads/24_gg_menu_add_pixel.png)

{% hint style="warning" %}
**Importante:** Para cada pixel / evento que você deseja disparar, você precisa ter uma conversão separada no Google Ads, ou seja, o ID do Pixel será o mesmo, mas o Label irá se alterar em cada conversão, dessa forma você deve configurar os eventos separadamente dentro da plataforma da GOPag.
{% endhint %}

**Veja um exemplo:**
Um produtor adicionou a conversão “Iniciate checkout” no Google Ads. Já na plataforma da GOPag ele deve adicionar as informações dessa conversão e selecionar apenas a opção “Disparar ao visitar o Checkout”. Caso selecione mais opções, isso irá interferir no disparo dos eventos.

Para as outras conversões, basta seguir o mesmo passo a passo!

### Testando a integração com o Google Ads

Você pode baixar a extensão Google Tag Assistant para checar se os disparos estão sendo feitos corretamente.

https://get.google.com/tagassistant/