---
title: Permita que o criador do laboratório escolha a localização no Azure Lab Services
description: Este artigo descreve como um administrador de contas de laboratório pode permitir que os criadores de laboratórioescolham locais para seus laboratórios.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: 52d5628698d1f945a7f672595ee7ce4739b6d13c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444359"
---
# <a name="allow-lab-creator-to-pick-location-for-the-lab-in-azure-lab-services"></a>Permita que o criador do laboratório escolha o local para o laboratório no Azure Lab Services
No Azure Lab Services, um proprietário de conta de laboratório pode permitir que os criadores de laboratório (educadores) escolham um local para o laboratório que eles criam. Este local pode ser diferente da localização da conta do laboratório. Um local é um grupo de regiões azure. Por exemplo, a localização dos Estados Unidos é um grupo de regiões como o Leste dos EUA, Ocidente dos EUA, e assim por diante. 

Você, como proprietário de uma conta de laboratório, pode selecionar o criador do **laboratório permitir que escolha a** opção de localização do laboratório quando criar uma conta de laboratório e depois de criar a conta de laboratório (ou uma conta de laboratório existente). 

## <a name="at-the-time-of-lab-account-creation"></a>No momento da criação da conta de laboratório
Quando você cria uma conta de laboratório, você vê essa opção na primeira tela **(guia Básico).** 

![Habilite a opção no momento da criação do laboratório](../media/allow-lab-creator-pick-lab-location/create-lab-account.png)

Essa opção será desativada se você selecionar uma rede virtual de pares para sua conta de laboratório na guia **Avançado.**  

![Quando a rede virtual peer é ativada](../media/allow-lab-creator-pick-lab-location/peer-virtual-network.png)


## <a name="after-the-lab-account-is-created"></a>Depois que a conta do laboratório é criada
Depois de criar a conta de laboratório, você pode habilitar ou desativar essa opção seguindo estas etapas: 

1. Na página Conta do **Laboratório,** selecione **configurações de laboratório** no menu esquerdo.
2. Selecione o criador do **laboratório permitir que escolha a** opção de localização do laboratório se quiser permitir que o criador do laboratório selecione um local para o laboratório. Se for desativado, os laboratórios são automaticamente criados no mesmo local em que a conta do laboratório existe. 
    
    Este campo é desativado quando você seleciona uma rede virtual para o campo **de rede virtual Peer.** É porque os laboratórios na conta do laboratório devem estar na mesma região que o laboratório conta para que eles acessem recursos na rede virtual de pares. 
1. Selecione **Salvar** na barra de ferramentas. 

    ![Configurações de laboratório](../media/allow-lab-creator-pick-lab-location/lab-settings.png)

## <a name="no-virtual-network-and-location-selection-isnt-allowed"></a>Nenhuma seleção de rede virtual e localização não é permitida
Neste cenário, você não habilitou o criador do **laboratório permitir que escolhesse a** opção de localização do laboratório. 

![Sem localização de laboratório](../media/allow-lab-creator-pick-lab-location/lab-no-location.png)

Então, os criadores de laboratório (educadores) não vêem a opção de escolher um local para o laboratório. Eles verão o preço por hora para cada opção de tamanho disponível para eles. Quando eles criarem um laboratório, ele será criado em uma região do Azure que está no mesmo local que a região do Azure em que sua conta de laboratório está. Por exemplo, se a conta do laboratório estiver no **oeste dos EUA,** então o laboratório pode ser criado no **Centro-Sul dos EUA,** mas não seria criado no **Canadá Leste.** Nós não garantimos nada sobre a região que escolhemos além de que está no local. Se um tamanho estiver atualmente limitado, então o criador do laboratório verá uma caixa de seleção onde eles podem ver os tamanhos que normalmente suportamos, mas estão atualmente indisponíveis. 

## <a name="in-virtual-network-and-location-selection-isnt-allowed"></a>Na rede virtual e na seleção de localização não é permitido
Neste cenário, o criador do **laboratório Permitir que o criador do laboratório escolha a** opção de localização do laboratório é desativado porque você selecionou uma rede virtual de pares para a conta do laboratório. Em seguida, os criadores de laboratório verão a mesma tela que a opção anterior. Como todas as VMs devem estar na mesma região do Azure que a rede virtual, o laboratório será criado na mesma região do Azure em que a rede virtual está. Se essa região em particular for restrita para um tamanho, o tamanho aparecerá como indisponível. 

## <a name="location-selection-is-enabled"></a>A seleção de localização está ativada
Quando você seleciona **Permitir que o criador do laboratório escolha a localização do laboratório,** os criadores de laboratório (educadores) veem uma opção para selecionar um local ao criar um laboratório. 

![Escolha uma localização de laboratório](../media/allow-lab-creator-pick-lab-location/location-selection.png)

Os criadores de laboratórios vêem a faixa de preços para todos os locais em que o tamanho está, e podem escolher um Local. O laboratório será criado em qualquer região do Azure que mapeie esse local.

Se um local estiver constrangido, ele não será mostrado na lista por padrão. Expanda a lista de paradas e selecione **Mostrar locais indisponíveis para este tamanho**. 

![Mostrar locais indisponíveis](../media/allow-lab-creator-pick-lab-location/show-unavailable-locations.png)

## <a name="cost"></a>Custo
Anteriormente, o preço era baseado no tamanho da VM que você escolhe para o laboratório. Agora, o preço é baseado na combinação de Sistema Operacional (SO), Tamanho e localização. 

## <a name="next-steps"></a>Próximas etapas
Veja os artigos a seguir:

- [Conecte a rede do seu laboratório com uma rede virtual de pares](how-to-connect-peer-virtual-network.md)
- [Anexar uma galeria de imagens compartilhadas a um laboratório](how-to-attach-detach-shared-image-gallery.md)
- [Adicione um usuário como proprietário de laboratório](how-to-add-user-lab-owner.md)
- [Exibir configurações de firewall para um laboratório](how-to-configure-firewall-settings.md)
- [Configure outras configurações para um laboratório](how-to-configure-lab-accounts.md)