---
author: dcurwin
ms.service: backup
ms.topic: include
ms.date: 03/12/2020
ms.author: dacurwin
ms.openlocfilehash: 024f70a21b36bffa662ba6f568adc8efbd0a909d
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81539411"
---
Agora você pode usar [pontos finais privados](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) para fazer backup de seus dados com segurança de servidores dentro de uma rede virtual para o cofre dos Serviços de Recuperação. O ponto final privado usa um IP do espaço de endereço VNET para o seu cofre. O tráfego de rede entre seus recursos dentro da rede virtual e o cofre viaja pela sua rede virtual e um link privado na rede backbone da Microsoft. Isso elimina a exposição da internet pública. Os Private Endpoints podem ser usados para fazer backup e restaurar seus bancos de dados SQL e SAP HANA que são executados dentro de suas VMs Azure. Ele também pode ser usado para seus servidores locais usando o agente MARS.

O backup do Azure VM não requer conectividade à internet e, portanto, não requer Pontos finais privados para permitir o isolamento da rede.

>[!NOTE]
> Este recurso está atualmente em disponibilidade limitada, e está disponível no Centro-Oeste dos EUA, Centro-Sul dos EUA, Oeste dos EUA 2 e Leste dos EUA (disponibilidade em outras regiões do Azure a seguir). Preencha [esta pesquisa](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUQk5EQ1QxRzVOWDNDS1Y1Q0xLTkdLQ0U0RC4u) e envie azbackupnetsec@microsoft.com um e-mail para se você estiver interessado em usar private endpoints para backup do Azure. A capacidade de usar esse recurso está sujeita à aprovação do serviço de backup do Azure.
