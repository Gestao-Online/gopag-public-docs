# 🔵 Pixel do Taboola

### Como adicionar o pixel do Taboola

A integração da plataforma da GOPag com a Taboola funciona através do disparo de eventos.

Você precisa criar os eventos na sua conta do Taboola (cada evento terá um nome único), e em seguida cadastrar esses eventos nas configurações do produto da plataforma da GOPag.

O primeiro passo é pegar o seu Account ID. Vamos precisar disso para instalar o Pixel.

### Pegando o Account ID Taboola

Faça o login na sua conta do Taboola. Você então poderá ver o Account ID no canto direito superior da tela.

![](/assets/ads/35_taboola_pixel.png)

**Exemplo:**

Account ID: 1386930

### Criando o Pixel Taboola

Se você ainda não tem um Pixel taboola, vá ao menu **Tracking**, e clique em **Create Pixel**.

Você não precisa copiar o código do seu Pixel agora, pois usaremos o seu **Account ID**.

### Criando os eventos de conversão

Agora você pode criar os eventos de conversão no Taboola.

Basta acessar o menu **Tracking -> New Conversion**

No tipo de conversão, selecione a opção **Event**

![](/assets/ads/36_taboola_pixel_event.png)

Você pode selecionar um nome de evento pré-existente ou criar um novo, e configurar opções como, por exemplo, um valor fixo da conversão.

Preste atenção, você precisará cadastrar o **Event Name** na plataforma da GOPag, então copie ele para um bloco de notas.

![](/assets/ads/37_taboola_pixel_event_name.png)

Basta então salvar a conversão recém criada no Taboola no fim da página.

### Cadastrando os eventos de conversão na plataforma da GOPag

{% hint style="warning" %}
**Importante:** Você pode cadastrar até 5 pixels/eventos de conversão por produto na plataforma da GOPag.
{% endhint %}

Acesse a aba de **Configurações** do seu produto e desça a tela até a seção de **Pixels**, depois clique em Taboola.

Clique em adicionar, e preencha as informações do evento que acabou de gerar.

![](/assets/ads/38_taboola_pixel_add.png)

Você pode adicionar até 5 eventos por produto, com nomes e combinações diferentes. Basta repetir o processo e configurar cada um.

Por exemplo, pode ter um evento para visitas ao checkout, e outro para compras aprovadas.

### Testando o Pixel Taboola

Você pode utilizar a própria extensão do Taboola para Google Chrome para testar as conversões:

[https://chrome.google.com/webstore/detail/taboola-pixel-helper/aefiepimkogajhddmhcekceihikjcabd](https://chrome.google.com/webstore/detail/taboola-pixel-helper/aefiepimkogajhddmhcekceihikjcabd)

<br>
<br>