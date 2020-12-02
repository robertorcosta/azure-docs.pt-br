---
title: Introdução às Filas do Azure - Armazenamento do Azure
description: Veja uma introdução às Filas do Azure, um serviço para armazenar um grande número de mensagens. Um serviço fila contém um formato de URL, uma conta de armazenamento, uma fila e uma mensagem.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/18/2020
ms.service: storage
ms.subservice: queues
ms.topic: overview
ms.reviewer: dineshm
ms.openlocfilehash: cb9d25bc9449c96ec7bf5ba11f8d64d59c8ddb4d
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96491937"
---
# <a name="what-are-azure-queues"></a>O que são as Filas do Azure?

O armazenamento de Filas do Azure é um serviço usado para armazenar grandes quantidades de mensagens. Você acessa as mensagens em qualquer lugar do mundo por meio de chamadas autenticadas usando HTTP ou HTTPS. Uma mensagem da fila pode ter até 64 KB. Uma fila pode conter milhões de mensagens, até o limite da capacidade total de uma conta de armazenamento. As filas são normalmente usadas para criar uma lista de pendências de trabalho para processamento assíncrono.

## <a name="queue-service-concepts"></a>Conceitos do serviço Fila

O serviço Fila contém os seguintes componentes:

![Diagrama mostrando a relação entre uma conta de armazenamento, as filas e as mensagens.](./media/storage-queues-introduction/queue1.png)

- **Formato da URL:** as filas são acessadas usando o seguinte formato de URL:

  `https://<storage account>.queue.core.windows.net/<queue>`

  A URL a seguir endereça um fila no diagrama:

  `https://myaccount.queue.core.windows.net/images-to-download`

- **Conta de armazenamento:** Todo o acesso ao Armazenamento do Azure ocorre por meio de uma conta de armazenamento. Para obter informações sobre a capacidade da conta de armazenamento, confira [Metas de escalabilidade e desempenho das contas de Armazenamento Standard](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).

- **Fila:** uma fila contém um conjunto de mensagens. O nome da fila **precisa** estar em letras minúsculas. Para saber mais sobre filas de nomenclatura, confira [Nomenclatura de filas e metadados](/rest/api/storageservices/Naming-Queues-and-Metadata).

- **Mensagem:** uma mensagem, em qualquer formato, de até 64 KB. Antes da versão 2017-07-29, a vida útil máxima permitida era de sete dias. Para a versão 2017-07-29 ou posterior, a vida útil máxima pode ser qualquer número positivo ou -1, indicando que a mensagem não expira. Se esse parâmetro for omitido, a vida útil padrão será de sete dias.

## <a name="next-steps"></a>Próximas etapas

- [Criar uma conta de armazenamento](../common/storage-account-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
- [Introdução às filas usando .NET](storage-dotnet-how-to-use-queues.md)
