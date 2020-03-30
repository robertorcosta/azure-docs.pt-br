---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.custom: seo-python-october2019
ms.openlocfilehash: 23b93f507ef6abe19a0202b28afa31d28490b2b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80151308"
---
## <a name="what-is-queue-storage"></a>O que é armazenamento na fila?

O armazenamento de filas do Azure é um serviço para armazenamento de um grande número de mensagens que podem ser acessadas de qualquer lugar do mundo por meio de chamadas autenticadas usando HTTP ou HTTPS. Uma única mensagem de fila pode ter até 64 KB de tamanho e uma fila pode conter milhões de mensagens, até o limite de capacidade total de uma conta de armazenamento. O armazenamento na fila é frequentemente usado para criar um backlog de trabalho para processar assíncronamente.

## <a name="queue-service-concepts"></a>Conceitos do serviço Fila

O serviço Azure Queue contém os seguintes componentes:

![Componentes do serviço Azure Fila](./media/storage-queue-concepts-include/azure-queue-service-components.png)

* **Formato de URL:** As filas são endereçadas usando`<storage account>`o seguinte formato de URL: http:// .queue.core.windows.net/`<queue>`
  
    A URL a seguir endereça um fila no diagrama:  
  
    `http://myaccount.queue.core.windows.net/incoming-orders`

* **Conta de armazenamento:** Todo o acesso ao Azure Storage é feito através de uma conta de armazenamento. Para saber mais sobre as contas de armazenamento, confira [Visão geral da conta de armazenamento](../articles/storage/common/storage-account-overview.md).
* **Fila:** uma fila contém um conjunto de mensagens. Todas as mensagens devem estar em uma fila. Observe que o nome da fila deve estar em letras minúsculas. Para saber mais sobre filas de nomenclatura, confira [Nomenclatura de filas e metadados](https://msdn.microsoft.com/library/azure/dd179349.aspx).
* **Mensagem:** uma mensagem, em qualquer formato, de até 64 KB. O tempo máximo que uma mensagem pode ficar na fila é de sete dias. Para a versão 2017-07-29 ou posterior, a vida útil máxima pode ser qualquer número positivo ou -1, indicando que a mensagem não expira. Se esse parâmetro for omitido, a vida útil padrão será de sete dias.

