---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/09/2020
ms.author: tamram
ms.openlocfilehash: 27617da97ced9ac775beae71e4c25202944b2aba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78940912"
---
A tabela a seguir descreve metas de capacidade, escalabilidade e desempenho para armazenamento de tabela.

| Recurso | Destino |
|----------|---------------|
| Número de tabelas em uma conta de armazenamento do Azure | Limitado apenas pela capacidade da conta de armazenamento |
| Número de partições em uma tabela | Limitado apenas pela capacidade da conta de armazenamento |
| Número de entidades em uma partição | Limitado apenas pela capacidade da conta de armazenamento |
| Tamanho máximo de uma única tabela | 500 TiB |
| Tamanho máximo de uma única entidade, incluindo todos os valores de propriedade | 1 MiB |
| Número máximo de propriedades em uma entidade de tabela | 255 (incluindo as três propriedades do sistema, **PartitionKey,** **RowKey**e **Timestamp)** |
| Tamanho total máximo de uma propriedade individual em uma entidade | Varia de acordo com o tipo de propriedade. Para obter mais informações, consulte **Tipos de propriedades** na [compreensão do modelo de dados de serviço de tabela](/rest/api/storageservices/understanding-the-table-service-data-model). |
| Tamanho do **PartitionKey** | Uma corda de até 1 KiB em tamanho |
| Tamanho do **RowKey** | Uma corda de até 1 KiB em tamanho |
| Tamanho de uma transação de grupo de entidades | Uma transação pode incluir no máximo 100 entidades e a carga deve ter menos de 4 MiB de tamanho. Uma transação de grupo de entidades pode incluir uma atualização para uma entidade apenas uma vez. |
| Número máximo de políticas de acesso armazenadas por tabela | 5 |
| Taxa máxima de solicitação por conta de armazenamento | 20.000 transações por segundo, o que pressupõe um tamanho de entidade 1-KiB |
| Throughput de destino para uma única partição de tabela (1 kib-entidades) | Até 2.000 entidades por segundo |