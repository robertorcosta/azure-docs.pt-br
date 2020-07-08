---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/10/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 427117fe47294a1db1fa8d3fa1e46ee1efb91b4d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "79129108"
---
## <a name="limitations"></a>Limitações

- Atualmente, não há suporte para conjuntos de dimensionamento de máquinas virtuais em hosts dedicados.
- Os tamanhos e tipos de hardware disponíveis para hosts dedicados variam de acordo com a região. Consulte a [página de preços](https://aka.ms/ADHPricing) do host para saber mais.

## <a name="create-a-host-group"></a>Criar um grupo de hosts

Um **grupo de hosts** é um novo recurso que representa uma coleção de hosts dedicados. Você cria um grupo de hosts em uma região e uma zona de disponibilidade e adiciona hosts a ele. Ao planejar a alta disponibilidade, há opções adicionais. Você pode usar uma ou ambas as opções a seguir com hosts dedicados: 
- Alcance de várias zonas de disponibilidade. Nesse caso, é necessário ter um grupo de hosts em cada uma das zonas que você quer usar.
- Alcance de vários domínios de falha que são mapeados para racks físicos. 
 
Em ambos os casos, você precisa fornecer a contagem de domínios de falha ao seu grupo de hosts. Se você não quiser abranger domínios de falha no seu grupo, use uma contagem de domínio de falha de 1. 

Você também pode optar por usar tanto zonas de disponibilidade quanto domínios de falha. 

Neste exemplo, criaremos um grupo de hosts usando 1 zona de disponibilidade e 2 domínios de falha. 


1. Abra o [portal](https://portal.azure.com)do Azure.
1. Selecione **criar um recurso** no canto superior esquerdo.
1. Procure por **grupo de hosts** e, em seguida, selecione **grupos de hosts** nos resultados.
1. Na página **grupos de hosts** , selecione **criar**.
1. Selecione a assinatura que você deseja usar e, em seguida, selecione **criar nova** para criar um novo grupo de recursos.
1. Digite *myDedicatedHostsRG* como o **nome** e, em seguida, selecione **OK**.
1. Para **nome do grupo de hosts**, digite *myhost*Group.
1. Em **Localização**, selecione **Leste dos EUA**.
1. Para **zona de disponibilidade**, selecione **1**.
1. Para **contagem de domínios de falha**, selecione **2**.
1. Selecione **revisar + criar** e aguarde a validação.
1. Depois de ver a mensagem **validação aprovada** , selecione **criar** para criar o grupo de hosts.

Só deve levar alguns minutos para criar o grupo de hosts.

## <a name="create-a-dedicated-host"></a>Criar um host dedicado

Agora, crie um host dedicado no grupo de hosts. Além de um nome para o host, você deve fornecer o SKU para o host. O SKU do host captura a série de VMs com suporte, bem como a geração de hardware para o host dedicado.

Para mais informações sobre preços e SKUs do host, consulte [Preços do Host Dedicado do Azure](https://aka.ms/ADHPricing).

Ao definir uma contagem de domínios de falha para seu grupo de hosts, você será solicitado a especificar o domínio de falha para o host.  

1. Selecione **criar um recurso** no canto superior esquerdo.
1. Pesquise **host dedicado** e, em seguida, selecione **hosts dedicados** nos resultados.
1. Na página **hosts dedicados** , selecione **criar**.
1. Selecione a assinatura que você deseja usar.
1. Selecione *myDedicatedHostsRG* como o **grupo de recursos**.
1. Em **detalhes da instância**, digite *myhost* para o **nome** e selecione *leste dos EUA* para o local.
1. Em **perfil de hardware**, selecione *família de Es3 padrão-tipo 1* para a **família de tamanho**, selecione *myhost* Group para o **grupo de hosts** e, em seguida, selecione *1* para o **domínio de falha**. Deixe os padrões para o restante dos campos.
1. Quando terminar, selecione **revisar + criar** e aguarde a validação.
1. Depois de ver a mensagem **validação aprovada** , selecione **criar** para criar o host.


