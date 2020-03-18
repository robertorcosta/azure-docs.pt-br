---
author: dcurwin
ms.service: backup
ms.topic: include
ms.date: 03/12/2020
ms.author: dacurwin
ms.openlocfilehash: a3bae0ce3e6ebcf64936d0ca4af4fb702e5ea404
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2020
ms.locfileid: "79137017"
---
Agora, você pode usar [Pontos de Extremidade Privados](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) para fazer backup de dados de maneira segura de servidores dentro de uma rede virtual para seu cofre dos Serviços de Recuperação. O ponto de extremidade privado usa um IP do espaço de endereço da VNET para o cofre. O tráfego de rede entre seus recursos dentro da rede virtual e o cofre passa pela rede virtual e por um link privado na rede de backbone da Microsoft. Isso elimina a exposição à Internet pública. Os Pontos de Extremidade Privados podem ser usados para fazer backup e restaurar os bancos de dados SQL e SAP HANA executados dentro de suas VMs do Azure. Eles também podem ser usados para servidores locais que usam o agente MARS.

O backup da VM do Azure não requer conectividade com a Internet e, portanto, não requer Pontos de Extremidade Privados para permitir o isolamento de rede.

>[!NOTE]
> Atualmente, esse recurso está na fase de uso inicial. Preencha [esta pesquisa](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUQk5EQ1QxRzVOWDNDS1Y1Q0xLTkdLQ0U0RC4u) e envie um email para azbackupnetsec@microsoft.com se estiver interessado em usar Pontos de Extremidade Privados para o Backup do Azure. A capacidade de usar esse recurso está sujeita à aprovação do serviço de Backup do Azure.
