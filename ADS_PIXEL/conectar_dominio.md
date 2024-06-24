# Como conectar o domínio verificado do Facebook Ads

Devido as novas atualizações do iOS 14, o Facebook exige que o seu pixel de conversão seja disparado no domínio do seu site (que deve ser verificado no Facebook).

Você pode conectar esse mesmo domínio (ou vários) a pltaforma da GOPag, para que o pixel seja disparado nele, e não ocorra nenhum problema de rastreio no Facebook.

A pltaforma da GOPag pode fazer o disparo do Pixel dentro do seu domínio, através de um subdomínio no formato pixels.seusite.com

### Como conectar meu domínio na plataforma da GOPag

Dentro de Editar produto -> Configurações, na parte de Pixels de conversão, clique no link Gerenciar domínios Facebook

![](/assets/ads/08_fb_menu_dominio.png)

Em seguida clique em Adicionar domínio

![](/assets/ads/09_fb_menu_add_dominio.png)

Preencha o seu domínio base

![](/assets/ads/10_fb_menu_add_dominio_link.png)

### Configurando o DNS

Agora vá no lugar onde você registrou o seu domínio, ou onde gerencia o DNS (GoDaddy, Namecheap, Cloudflare, etc...)

Crie uma entrada CNAME, com valor pixels, apontando para pixels.gopag-stage.com.br

![Ocampo "TTL" será preenchido de acordo com a pltaforma de registro que estiver utilizando. Ou seja, não será necessariamente "auto".](/assets/ads/11_fb_menu_add_dominio_dns.png)

{% hint style="warning" %}
**Importante:** Caso você use Cloudflare, crie a entrada sem proxy, ou seja "apenas DNS".
{% endhint %}

![](/assets/ads/12_fb_menu_add_dominio_proxy.png)

Pronto, agora pode voltar para a plataforma da GOPag e salvar o domínio que acabou de adicionar

![](/assets/ads/13_fb_menu_add_dominio_proxy_confirm.png)

### Verificando o seu domínio

Ao adicionar um domínio, ele já pode ser usado para disparar os Pixels, a verificação não é obrigatória.

Se você quer verificar que o domínio foi configurado corretamente, clique sobre ele:

![](/assets/ads/14_fb_menu_add_dominio_verificar.png)

Em seguida, clique em Verificar domínio. Pode levar 15-20 segundos carregando.

![](/assets/ads/15_fb_menu_add_dominio_verificar_tempo.png)

Se o ícone mudar para a cor verde, o domínio foi configurado com sucesso.

![Pode levar algumas horas até que o DNS propague e seja possível verificar o domínio](/assets/ads/16_fb_menu_add_dominio_verificado.png)

{% hint style="warning" %}
**Importante:** Se posteriormente você alterar os nameservers do domínio, ou alterar / remover a entrada CNAME que criou, o pixel irá parar de funcionar.
{% endhint %}

### Verificando se a entrada DNS foi adicionada corretamente

Para conferir se está tudo certo é bem simples! Primeiro, acesse o site da [MX toolbox](https://mxtoolbox.com/)

Preencha a barra de pesquisa dessa forma: pixels.seudomínio.com.br trocando "seudomínio.com.br" pelo que está utilizando.

Se a configuração estiver correta, estará marcado com um certinho verde e apontando para a plataforma da GOPag!

![Você também pode conferir em que site o domínio está registrado!](/assets/ads/17_fb_menu_add_dominio_mx_toolbox.png)

### Utilizando o seu domínio para disparar Pixels

Na parte de configuração dos Pixels do Facebook, selecione o domínio onde quer que o Pixel dispare.

![](/assets/ads/18_fb_menu_add_dominio_disparos.png)

Diferentes Pixels podem disparar em diferentes domínios, ou no mesmo domínio. Você tem total controle.

[Clique aqui para ler o nosso artigo mais completo sobre como instalar o Pixel do Facebook](/ADS_PIXEL/facebook_ads.md)

### Sou afiliado, preciso ter um domínio próprio para instalar o Pixel do Facebook?

Sim, para que os eventos sejam disparados corretamente você precisa ter a sua própria estrutura, como a página de vendas.