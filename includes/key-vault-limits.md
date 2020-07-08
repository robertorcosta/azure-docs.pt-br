---
author: rothja
ms.service: key-vault
ms.topic: include
ms.date: 04/21/2020
ms.author: jroth
ms.openlocfilehash: 01b3c9584f3ecddbcdcc6938f5eb469510a47a4e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85838996"
---
### <a name="key-transactions-maximum-transactions-allowed-in-10-seconds-per-vault-per-regionsup1sup"></a>Transações de chave (máximo de transações permitidas em 10 segundos, por cofre por região<sup>1</sup>):

|Tipo de chave|Chave HSM<br>CRIAR chave|Chave HSM<br>Todas as outras transações|Chave de software<br>CRIAR chave|Chave de software<br>Todas as outras transações|
|:---|---:|---:|---:|---:|
|RSA de 2.048 bits|5|1,000|10|2\.000|
|RSA de 3.072 bits|5|250|10|500|
|RSA de 4.096 bits|5|125|10|250|
|ECC P-256|5|1,000|10|2\.000|
|ECC P-384|5|1,000|10|2\.000|
|ECC P-521|5|1,000|10|2\.000|
|ECC SECP256K1|5|1,000|10|2\.000|

> [!NOTE]
> Na tabela anterior, vemos que, para chaves de software RSA de 2.048 bits, são permitidas 2.000 transações GET por 10 segundos. Para chaves RSA de 2.048 bits HSM-Keys, são permitidas 1.000 transações GET por 10 segundos.
>
> Os limites de limitação são ponderados e a imposição está em sua soma. Por exemplo, conforme mostrado na tabela anterior, quando você executa operações GET em chaves RSA HSM, é oito vezes mais caro usar chaves de 4.096 bits comparadas com chaves de 2.048 bits. Isso ocorre porque 1000/125 = 8.
>
> Em um determinado intervalo de 10 segundos, um cliente Azure Key Vault pode fazer *apenas uma* das seguintes operações antes de encontrar um `429` código de status HTTP de limitação:
> - 2.000 RSA 2.048-bit software-chave obter transações
> - 1.000 RSA 2.048 bits HSM-chave obter transações
> - 125 RSA 4.096 bits HSM-chave obter transações
> - 124 RSA 4.096 bits HSM-Key GET Transactions e 8 RSA 2.048-bit HSM-Key GET Transactions

### <a name="secrets-managed-storage-account-keys-and-vault-transactions"></a>Segredos, chaves de conta de armazenamento gerenciado e transações de cofre:

| Tipo de transação | Máximo de transações permitidas em 10 segundos, por cofre por região<sup>1</sup> |
| --- | --- |
| Todas as transações |2\.000 |

Para obter informações sobre como lidar com a limitação quando esses limites são excedidos, consulte [diretrizes de limitação de Azure Key Vault](../articles/key-vault/key-vault-ovw-throttling.md).

<sup>1</sup> um limite de toda a assinatura para todos os tipos de transação é cinco vezes por limite de cofre de chaves. Por exemplo, HSM-outras transações por assinatura são limitadas a 5.000 transações em 10 segundos por assinatura.

### <a name="azure-private-link-integration"></a>Integração de link privado do Azure

> [!NOTE]
> O número de cofres de chaves com pontos de extremidade privados habilitados por assinatura é um limite ajustável. Esse limite mostrado abaixo é o limite padrão. Se você quiser solicitar um aumento de limite para o serviço, envie um email para akv-privatelink@microsoft.com. Aprovaremos essas solicitações dependendo do caso.

| Recurso | Limite |
| -------- | ----- |
| Pontos de extremidade privados por cofre de chaves | 64 |
| Cofres de chaves com pontos de extremidade privados por assinatura | 400 |
