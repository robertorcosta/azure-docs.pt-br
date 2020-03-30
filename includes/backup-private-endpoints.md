---
author: dcurwin
ms.service: backup
ms.topic: include
ms.date: 03/12/2020
ms.author: dacurwin
ms.openlocfilehash: a3bae0ce3e6ebcf64936d0ca4af4fb702e5ea404
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137017"
---
Agora você pode usar [pontos finais privados](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) para fazer backup de seus dados com segurança de servidores dentro de uma rede virtual para o cofre dos Serviços de Recuperação. O ponto final privado usa um IP do espaço de endereço VNET para o seu cofre. O tráfego de rede entre seus recursos dentro da rede virtual e o cofre viaja pela sua rede virtual e um link privado na rede backbone da Microsoft. Isso elimina a exposição da internet pública. Os Private Endpoints podem ser usados para fazer backup e restaurar seus bancos de dados SQL e SAP HANA que são executados dentro de suas VMs Azure. Ele também pode ser usado para seus servidores locais usando o agente MARS.

O backup do Azure VM não requer conectividade à internet e, portanto, não requer Pontos finais privados para permitir o isolamento da rede.

>[!NOTE]
> Este recurso está atualmente em uso inicial. Preencha [esta pesquisa](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUQk5EQ1QxRzVOWDNDS1Y1Q0xLTkdLQ0U0RC4u) e envie azbackupnetsec@microsoft.com um e-mail para se você estiver interessado em usar private endpoints para backup do Azure. A capacidade de usar esse recurso está sujeita à aprovação do serviço de backup do Azure.
