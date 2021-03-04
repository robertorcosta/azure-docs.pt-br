---
title: Conector de Gerenciamento de Serviços de TI no Azure Monitor
description: Este artigo fornece informações sobre como conectar seus produtos/serviços de ITSM com o Conector de Gerenciamento de Serviço de TI (ITSMC) no Azure Monitor para monitorar e gerenciar os itens de trabalho ITSM de forma centralizada.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/12/2020
ms.openlocfilehash: 046fcb9d7473de5666b3acb25cbcaa1f9549e679
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102039487"
---
# <a name="connect-itsm-productsservices-with-it-service-management-connector"></a>Conectar produtos/serviços de ITSM ao Conector de Gerenciamento de Serviços de TI
Este artigo fornece informações sobre como configurar a conexão entre seu produto/serviço de ITSM e o ITSMC (Conector de Gerenciamento de Serviços de TI) no Log Analytics para gerenciar seus itens de trabalho de forma centralizada. Para obter mais informações sobre o ITSMC, consulte [Visão geral](./itsmc-overview.md).

Há suporte para os seguintes produtos/serviços ITSM. Selecione o produto para exibir informações detalhadas sobre como conectar o produto ao ITSMC.

- [ServiceNow](./itsmc-connections-servicenow.md)
- [System Center Service Manager](./itsmc-connections-scsm.md)
- [Cherwell](./itsmc-connections-cherwell.md)
- [Provance](./itsmc-connections-provance.md)

> [!NOTE]
> Sugerimos que nossos clientes Cherwell e Provance usem a [ação de webhook](./action-groups.md#webhook) para o ponto de extremidade Cherwell e Provance como outra solução para a integração.

## <a name="next-steps"></a>Próximas etapas

* [Solução de problemas no Conector ITSM](./itsmc-resync-servicenow.md)