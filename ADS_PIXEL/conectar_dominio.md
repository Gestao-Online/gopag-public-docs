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

![](/assets/ads/11_fb_menu_add_dominio_dns.png)

