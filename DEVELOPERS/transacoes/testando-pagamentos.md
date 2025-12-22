# Testando pagamentos

Para testar pagamentos bem sucedidos em nossa API, experimente um dos números de cartão abaixo.

## Quando Usar

Os seguintes números de cartão de crédito podem ser usados para simular transações em ambiente de teste \(sandbox\), para pagamentos bem-sucedidos:

| Número | Bandeira |
| :--- | :--- |
| 4539003370725497 | Visa \(Digitada\) |
| 4761340000000035 | Visa \(Chip & PIN\) |
| 4716588836362104 | Visa \(Crédito\) |
| 4532650104137832 | Visa Electron \(Crédito\) |
| 5356066320271893 | MasterCard \(Digitada\) |
| 5201561050024014 | MasterCard \(Chip & PIN\) |
| 5577270004286630 | MasterCard \(Crédito\) |
| 5138692036125449 | MasterCard \(Crédito\) |

Além disso, esses são os números "mágicos" de cartões que gerarão respostas específicas, úteis para testar diferentes cenários:

| Número | Bandeira |
| :--- | :--- |
| 6011457819940087 | A transação será recusada com um código de "card\_declined". |
| 4929710426637678 | A transação será recusada com um código "card\_declined". |
| 4710426743216178 | A transação será recusada com um código "service\_request\_timeout". |

