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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79129108"
---
## <a name="limitations"></a>Limitações

- Os conjuntos de escala de máquinas virtuais não são suportados atualmente em hosts dedicados.
- Os tamanhos e tipos de hardware disponíveis para hosts dedicados variam de acordo com a região. Consulte a [página de preços](https://aka.ms/ADHPricing) do host para saber mais.

## <a name="create-a-host-group"></a>Criar um grupo de hosts

Um **grupo anfitrião** é um novo recurso que representa uma coleção de hosts dedicados. Você cria um grupo de host em uma região e uma região de disponibilidade e adiciona hosts a ele. Ao planejar a alta disponibilidade, há opções adicionais. Você pode usar uma ou ambas as opções a seguir com seus hosts dedicados: 
- Span em várias zonas de disponibilidade. Neste caso, você é obrigado a ter um grupo de hospedagem em cada uma das zonas que deseja usar.
- Spane vários domínios de falha que são mapeados para racks físicos. 
 
Em ambos os casos, você precisa fornecer a contagem de domínio de falha para o seu grupo de host. Se você não quiser cobrir domínios de falha em seu grupo, use uma contagem de domínios de falha de 1. 

Você também pode decidir usar zonas de disponibilidade e domínios de falhas. 

Neste exemplo, criaremos um grupo de host usando 1 zona de disponibilidade e 2 domínios de falha. 


1. Abra o [portal](https://portal.azure.com)Azure.
1. Selecione **Criar um recurso** no canto superior esquerdo.
1. Procure o **grupo Host** e, em seguida, selecione Grupos de **Host** a partir dos resultados.
1. Na página **Grupos de host,** selecione **Criar**.
1. Selecione a assinatura que você gostaria de usar e, em seguida, selecione **Criar novo** para criar um novo grupo de recursos.
1. Digite *myDedicatedHostsRG* como **nome** e, em seguida, selecione **OK**.
1. Para **o nome do grupo Host,** digite *myHostGroup*.
1. Em **Localização**, selecione **Leste dos EUA**.
1. Para **Zona de Disponibilidade,** selecione **1**.
1. Para **contagem de domínio de falha,** selecione **2**.
1. Selecione **'Revisar + criar'** e, em seguida, aguardar a validação.
1. Depois de ver a mensagem **aprovada pela Validação,** selecione **Criar** para criar o grupo de host.

Deve levar apenas alguns momentos para criar o grupo anfitrião.

## <a name="create-a-dedicated-host"></a>Crie um host dedicado

Agora crie um host dedicado no grupo anfitrião. Além de um nome para o host, você é obrigado a fornecer o SKU para o host. O Host SKU captura a série VM suportada, bem como a geração de hardware para o seu host dedicado.

Para obter mais informações sobre as SKUs host e preços, consulte [os preços do Host dedicado do Azure](https://aka.ms/ADHPricing).

Se você definir uma contagem de domínio de falha para o seu grupo de host, será solicitado que você especifique o domínio de falha para o host.  

1. Selecione **Criar um recurso** no canto superior esquerdo.
1. Procure **o host dedicado** e selecione **hosts dedicados** a partir dos resultados.
1. Na página **Hosts dedicados,** selecione **Criar**.
1. Selecione a assinatura que você gostaria de usar.
1. Selecione *myDedicatedHostsRG* como o **grupo Resource**.
1. Em **detalhes de instância,** digite *myHost* para o **Nome** e selecione *East US* para a localização.
1. No **perfil hardware,** selecione *A família Padrão Es3 - Tipo 1* para a família **Tamanho,** selecione *myHostGroup* para o **grupo Host** e selecione *1* para o domínio **Falha**. Deixe os padrões para o resto dos campos.
1. Quando terminar, selecione **'Revisar + criar'** e aguardar a validação.
1. Depois de ver a mensagem **aprovada pela Validação,** selecione **Criar** para criar o host.


