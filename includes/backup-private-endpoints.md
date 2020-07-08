---
author: dcurwin
ms.service: backup
ms.topic: include
ms.date: 03/12/2020
ms.author: dacurwin
ms.openlocfilehash: b8941bb4fa77cb0008cb7271681e972bc21d6588
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83659528"
---
Agora, você pode usar [Pontos de Extremidade Privados](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) para fazer backup de dados de maneira segura de servidores dentro de uma rede virtual para seu cofre dos Serviços de Recuperação. O ponto de extremidade privado usa um IP do espaço de endereço da VNET para o cofre. O tráfego de rede entre seus recursos dentro da rede virtual e o cofre passa pela rede virtual e por um link privado na rede de backbone da Microsoft. Isso elimina a exposição à Internet pública. Os Pontos de Extremidade Privados podem ser usados para fazer backup e restaurar os bancos de dados SQL e SAP HANA executados dentro de suas VMs do Azure. Eles também podem ser usados para servidores locais que usam o agente MARS.

O backup da VM do Azure não requer conectividade com a Internet e, portanto, não requer Pontos de Extremidade Privados para permitir o isolamento de rede.

Leia mais sobre pontos de extremidade privados para o Backup do Azure [aqui](https://docs.microsoft.com/azure/backup/private-endpoints).
