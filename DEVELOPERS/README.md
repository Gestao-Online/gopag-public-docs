# GoPag API - Documentação

Bem-vindo à documentação oficial da GoPag API! Esta API foi desenvolvida para facilitar a integração de soluções de pagamento em sua aplicação.

## 🎯 Objetivo

Prover uma camada de abstração e segurança para acesso aos serviços de pagamento da GoPag, permitindo que:

- **Partner (Marketplaces)** integrem seus sistemas com a plataforma de pagamentos e gerenciem seus sellers e transações de forma isolada e segura
- **Clientes Finais** acessem funcionalidades de pagamento de forma simplificada

## 🔐 Modelo de Autenticação e Segurança

### Marketplace Token (Atual)
O sistema utiliza um modelo de **marketplace virtual** onde cada parceiro possui:

- **Token de Marketplace**: Autenticação via OAuth2 (Bearer token) com certificado mTLS
- **Escopo Limitado**: Acesso restrito apenas aos sellers dentro do escopo do partner
- **Marketplace Virtual**: Cada parceiro opera em seu próprio "marketplace" com gestão independente

## 🚀 Principais Funcionalidades

### 📊 Gestão de Transações
- Criação de transações (cartão, boleto, Pix)
- Consulta de transações individuais e em lote
- Captura de transações pré-autorizadas
- Histórico completo de transações por seller

### 🖥️ Gestão de Terminais
Gerenciamento de dispositivos de pagamento:
- **Físicos**: Maquininhas tradicionais
- **Virtuais**: 
  - Tap2Pay (pagamento por aproximação)
  - Aplicativos Android/iOS
- Pareamento de terminais
- Monitoramento de status

### 👥 Gestão de Sellers
- Listagem de sellers do marketplace
- Consulta de dados individuais
- Busca por CPF/CNPJ

### 💳 Gestão de Compradores (Buyers)
- CRUD completo de compradores
- Busca por CPF/CNPJ
- Validação de dados cadastrais

## 🔄 Fluxos de Pagamento Suportados

### Fluxo Tradicional
- **Cartão de Crédito/Débito**: Transações com captura imediata ou posterior
- **Boleto Bancário**: Geração e consulta de boletos
- **Pix**: Pagamentos instantâneos via QR Code ou Pix Copia e Cola

### Fluxo Inteligente 
- **Cadastro de Link de Pagamento**: Criação automatizada de métodos de pagamento
- **Tap2Pay**: Integração nativa para pagamentos por aproximação


## 📚 Índice

### Introdução e Aspectos Gerais
- [Introdução](./introducao/visao-geral.md)
- [Requisitos de Segurança](./introducao/requisitos-seguranca.md)
- [Autenticação](./introducao/autenticacao.md)
- [Testando a API](./introducao/testando.md)
- [Códigos de Erro](./introducao/codigos-erro.md)

### Cadastro e Credenciamento

#### Vendedores
- [Listando Vendedores](./cadastro/vendedores/listar.md)
- [Buscando Vendedor por CPF/CNPJ](./cadastro/vendedores/buscar-cpf-cnpj.md)
- [Recuperar Detalhes de Vendedor](./cadastro/vendedores/detalhes.md)
- [Categoria MCC (Merchant Category Codes)](./cadastro/vendedores/mcc.md)

#### Compradores
- [Gerenciar Compradores](./cadastro/compradores/compradores.md)

### Transações
- [Listar Transações](./transacoes/listar.md)
- [Recuperar Detalhes de Transação](./transacoes/detalhes.md)
- [Capturar Transação](./transacoes/capturar.md)
- [Tokenizar Cartão](./transacoes/tokenizar.md)

#### Criar Transação - Via API
- [Cartão de Crédito/Débito](./transacoes/criar/api/cartao.md)
- [PIX](./transacoes/criar/api/pix.md)
- [Boleto e Bolepix](./transacoes/criar/api/boleto.md)

#### Criar Transação - Payment Locales
- [MPOS](./transacoes/criar/mpos.md)
- [Tap to Pay (NFC)](./transacoes/criar/tap-to-pay.md)
- [PINPAD](./transacoes/criar/pinpad.md)
- [Link de Pagamento](./transacoes/criar/link-pagamento.md)

## 🚀 Começando

Para começar a usar a GoPag API, você precisará:

1. **Bearer Token**: Obtido via Portal GoPag ou enviado pela GoPag (Entre em contato para solicitar)
2. **Certificado mTLS**: Certificado ICP-Brasil para autenticação bidirecional
3. **Marketplace ID**: Identificador único do seu marketplace
4. **Ambiente**: URLs de homologação e produção

Consulte a seção [Autenticação](./introducao/autenticacao.md) para mais detalhes.

## 🔒 Segurança

A GoPag API utiliza:
- **Bearer Token**: Autenticação via OAuth 2.0
- **mTLS (Mutual TLS)**: Autenticação bidirecional com certificados ICP-Brasil
- **Isolamento por Marketplace**: Cada marketplace só acessa seus próprios dados

## 📞 Suporte

Para suporte técnico, dúvidas sobre integração ou solicitação de credenciamento:

📧 Email: suporte@gopag.com.br