---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: 1ab6243be39bf30bc060ed5745fbf600924743a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "71839202"
---
| Recurso | Limite |
| --- | --- |
| Tamanho do cache |1,2 TB |
| Bancos de dados |64 |
| Máximo de clientes conectados |40.000 |
| Cache do Azure para réplicas Redis, para alta disponibilidade |1 |
| Fragmentos em um cache premium com clustering |10 |

Os limites e tamanhos do Cache Redis do Azure são diferentes para cada camada de preços. Para ver os níveis de preços e seus tamanhos associados, consulte [O Cache do Azure para preços redis](https://azure.microsoft.com/pricing/details/cache/).

Para obter informações sobre os limites de configuração do Cache Redis do Azure, veja [Configuração padrão do servidor do Redis](../articles/azure-cache-for-redis/cache-configure.md#default-redis-server-configuration).

Como configuração e gerenciamento de instâncias do Cache Redis do Azure é feito pela Microsoft, nem todos os comandos do Redis são compatíveis no Cache Redis do Azure. Para obter mais informações, confira [Comandos Redis não têm suporte no Cache Redis do Azure](../articles/azure-cache-for-redis/cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis).

