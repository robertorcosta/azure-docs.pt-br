---
title: Conector de Gerenciamento de Serviços de TI no Azure Monitor
description: Este artigo fornece informações sobre como conectar seus produtos/serviços de ITSM com o Conector de Gerenciamento de Serviço de TI (ITSMC) no Azure Monitor para monitorar e gerenciar os itens de trabalho ITSM de forma centralizada.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/12/2020
ms.openlocfilehash: 7ac842df17e80b49990d89d1623330c4e31ab566
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100605828"
---
# <a name="connect-itsm-productsservices-with-it-service-management-connector"></a>Conectar produtos/serviços de ITSM ao Conector de Gerenciamento de Serviços de TI
Este artigo fornece informações sobre como configurar a conexão entre seu produto/serviço de ITSM e o ITSMC (Conector de Gerenciamento de Serviços de TI) no Log Analytics para gerenciar seus itens de trabalho de forma centralizada. Para obter mais informações sobre o ITSMC, consulte [Visão geral](../platform/itsmc-overview.md).

Há suporte para os seguintes produtos/serviços ITSM. Selecione o produto para exibir informações detalhadas sobre como conectar o produto ao ITSMC.

- [ServiceNow](../platform/itsmc-connections-servicenow.md)
- [System Center Service Manager](../platform/itsmc-connections-scsm.md)
- [Cherwell](../platform/itsmc-connections-cherwell.md)
- [Provance](../platform/itsmc-connections-provance.md)

> [!NOTE]
> Sugerimos que nossos clientes Cherwell e Provance usem a [ação de webhook](../platform/action-groups.md#webhook) para o ponto de extremidade Cherwell e Provance como outra solução para a integração.

## <a name="next-steps"></a>Próximas etapas

* [Solução de problemas no Conector ITSM](../platform/itsmc-resync-servicenow.md)