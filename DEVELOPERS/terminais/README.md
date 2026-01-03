# Terminais

## Introdução

A API de Terminals do GoPag permite gerenciar dispositivos de pagamento (mPOS, PINPAD, Tap to Pay) vinculados aos vendedores. Com esta API você pode:

- **Parear novos terminais** a um vendedor
- **Listar terminais** associados a um vendedor
- **Consultar detalhes** de terminais específicos
- **Monitorar status** e metadados dos dispositivos

## Pré-requisitos

Antes de começar, certifique-se de ter:

1. **Credenciais de API** (Client ID e Client Secret)
2. **Token de Autenticação** OAuth2 válido
3. **Marketplace ID** configurado
4. **Seller ID** do vendedor que receberá os terminais

## Tipos de Terminais

A API suporta os seguintes tipos de terminais:

| Tipo | Descrição | Exemplos |
|------|-----------|----------|
| `mpos` | Máquina de cartão móvel | PAX A920, Gertec GPOS700 |
| `pinpad` | Terminal fixo tradicional | Ingenico MOVE 5000, Verifone VX520 |
| `tap_to_pay` | Pagamento por aproximação via smartphone | iPhone com NFC, Android com NFC |
| `link_payment` | Link de pagamento via web/app | Interface web, QR Code |

## Status dos Terminais

| Status | Descrição |
|--------|-----------|
| `active` | Terminal ativo e operacional |
| `inactive` | Terminal desativado temporariamente |
| `pending` | Aguardando ativação |
| `blocked` | Terminal bloqueado por violação de segurança |

## Fluxo de Integração

1. **[Autenticação](../introducao/autenticacao.md)** - Obter token OAuth2
2. **[Parear Terminal](parear.md)** - Vincular novo dispositivo ao vendedor
3. **[Listar Terminais](listar.md)** - Consultar todos os terminais do vendedor
4. **[Buscar Terminal](buscar.md)** - Obter detalhes de um terminal específico

## Estrutura do Objeto Terminal

```json
{
  "id": "a1b2c3d4e5f6789012345678901234ab",
  "resource": "terminal",
  "seller": "00771bc0349847108f54e200dbc6c325",
  "serial_number": "PAX-SN-123456",
  "status": "active",
  "type": "mpos",
  "metadata": {
    "device_model": "PAX A920",
    "firmware_version": "1.0.5",
    "manufacturer": "PAX Technology",
    "last_connection": "2025-12-29T15:45:00+00:00"
  },
  "created_at": "2025-12-01T10:30:00+00:00",
  "updated_at": "2025-12-29T15:45:00+00:00"
}
```

## Segurança

- Todos os endpoints requerem autenticação OAuth2
- Apenas o partner que possui o seller pode acessar seus terminais
- Tokens de pareamento são de uso único e expiram em 24 horas
- Logs de auditoria são mantidos para todas as operações

## Próximos Passos

Comece pela [autenticação](../introducao/autenticacao.md) e depois siga para [parear seu primeiro terminal](parear.md).
