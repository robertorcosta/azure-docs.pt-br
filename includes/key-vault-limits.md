---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 2d16febd4676ca7ba763eb7bc6dcecda4608ebb5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "74224477"
---
#### <a name="key-transactions-maximum-transactions-allowed-in-10-seconds-per-vault-per-regionsup1sup"></a>Transações-chave (transações máximas permitidas em 10 segundos, por cofre por região<sup>1</sup>):

|Tipo de chave|Tecla HSM<br>Criar chave|Tecla HSM<br>Todas as outras transações|Chave de software<br>Criar chave|Chave de software<br>Todas as outras transações|
|:---|---:|---:|---:|---:|
|RSA 2.048 bits|5|1,000|10|2.000|
|RSA 3.072 bits|5|250|10|500|
|RSA 4.096 bits|5|125|10|250|
|ECC P-256|5|1,000|10|2.000|
|ECC P-384|5|1,000|10|2.000|
|ECC P-521|5|1,000|10|2.000|
|ECC SECP256K1|5|1,000|10|2.000|

> [!NOTE]
> Na tabela anterior, vemos que para chaves de software RSA de 2.048 bits, 2.000 transações GET por 10 segundos são permitidas. Para as chaves HSM de 2.048 bits RSA, são permitidas 1.000 transações GET por 10 segundos.
>
> Os limites de estrangulamento são ponderados, e a aplicação está em sua soma. Por exemplo, como mostrado na tabela anterior, quando você executa operações GET em teclas RSA HSM, é oito vezes mais caro usar teclas de 4.096 bits em comparação com teclas de 2.048 bits. Isso porque 1.000/125 = 8.
>
> Em um intervalo de 10 segundos, um cliente do Azure Key Vault pode `429` fazer apenas *uma* das seguintes operações antes de encontrar um código de status HTTP de estrangulamento:
> - 2.000 transações de 2.048 bits de software-key GET
> - 1.000 Transações de chave HSM de 2.048 bits
> - 125 Transações de chave HSM de 4.096 bits
> - 124 transações hsm-key GET de 4.096 bits e 8 transações hsm-key GET de 2.048 bits

#### <a name="secrets-managed-storage-account-keys-and-vault-transactions"></a>Segredos, chaves de conta de armazenamento gerenciada e transações de cofre:
| Tipo de transação | Transações máximas permitidas em 10 segundos, por cofre por região<sup>1</sup> |
| --- | --- |
| Todas as transações |2.000 |

Para obter informações sobre como lidar com o estrangulamento quando esses limites forem excedidos, consulte [a orientação de estrangulamento do Azure Key Vault](../articles/key-vault/key-vault-ovw-throttling.md).

<sup>1</sup> Um limite de cobertura de assinatura para todos os tipos de transações é de cinco vezes por limite de cofre de chave. Por exemplo, outras transações HSM por assinatura são limitadas a 5.000 transações em 10 segundos por assinatura.
