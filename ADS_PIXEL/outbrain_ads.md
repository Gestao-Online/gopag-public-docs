# 🟠 Pixel da Outbrain

### Como adicionar o pixel da Outbrain

A integração da plataforma da GOPag com a Outbrain funciona por meio do disparo de eventos.

Você precisa criar os eventos na sua conta da Outbrain (cada evento terá um nome único), e em seguida cadastrar esses eventos nas configurações do produto da plataforma da GOPag.

O primeiro passo é pegar o ID do seu pixel Outbrain. Ele se chama OB_ADV_ID.

### Pegando o ID do Pixel Outbrain (OB_ADV_ID)

Faça o login na sua conta da Outbrain. Clique no menu **Conversions**, e em seguida em **Install Pixel**.

![](/assets/ads/27_outbrain_pixel.png)
![](/assets/ads/28_outbrain_pixel_install.png)

Selecione a opção **Install pixel manually** e depois clique em **Install**.

![](/assets/ads/29_outbrain_pixel_install_manual.png)

Você precisará então extrair o **OB_ADV_ID** do código do Pixel.

![](/assets/ads/30_outbrain_pixel_install_cod.png)

Recomendamos que você copie o pixel inteiro para um bloco de notas no seu computador, e depois extraia o **OB_ADV_ID**.

No exemplo da imagem acima, o ID extraído seria: **005ac8c25cf8c87a79d79c832a914d0cc2**

### Criando os eventos na Outbrain

No menu **Conversions**, clique em Add Conversion.

Em Type selecione a opção **Event-based conversion** e abaixo, selecione a opção **Manual code**

![](/assets/ads/31_outbrain_pixel_eventos.png)

Escolha uma categoria para o evento de conversão e escreva um nome.

O nome do evento será o mesmo que você cadastrará na plataforma da GOPag

![](/assets/ads/32_outbrain_pixel_eventos_name.png)

No exemplo da imagem acima, o nome do evento criado foi **venda**, mas você pode escolher qualquer nome que quiser.

Por exemplo, você pode ter um nome de evento diferente para cada produto.

Clique em salvar no fim da tela

![](/assets/ads/33_outbrain_pixel_save.png)

Pronto, o evento foi criado na Outbrain, agora você precisa cadastrar o mesmo pixel e evento na plataforma da GOPag

### Cadastrando o pixel e eventos na plataforma da GOPag

Você pode cadastrar até 5 pixels/eventos de conversão por produto na plataforma da GOPag.

Basta ir em **Editar produto -> aba Configurações**, e descer a tela até a seção de Pixels, então clicar em Outbrain.

Clique em adicionar, e preencha as informações do pixel e evento que acabou de criar na Outbrain.

![](/assets/ads/34_outbrain_pixel_add.png)

Você pode adicionar até 5 eventos por produto, com nomes e combinações diferentes. Basta repetir o processo e configurar cada um.

Por exemplo, pode ter um evento para visitas ao checkout, e outro para compras aprovadas.

Você pode usar sempre o mesmo Pixel ID (OB_ADV_ID), e apenas criar diferentes eventos de conversão na Outbrain de acordo com os seus objetivos.

### Testando o pixel da Outbrain

Você pode usar a extensão Outbrain Pixel Tracker no Google Chrome. Faça o download abaixo:

[https://chrome.google.com/webstore/detail/outbrain-pixel-tracker](https://chrome.google.com/webstore/detail/outbrain-pixel-tracker/daebadnaphbiobojnpgcenlkgpihmbdc?hl=en)

<br>
<br>