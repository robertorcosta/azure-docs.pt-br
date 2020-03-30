---
title: Especificar imagens de mercado para um laboratório no Azure Lab Services
description: Este artigo mostra como especificar as imagens do Marketplace que o criador do laboratório pode usar para criar laboratórios em uma conta de laboratório no Azure Lab Services.
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
ms.openlocfilehash: ad56041f853d030e3a286610fe4872bffecaee12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444661"
---
# <a name="specify-marketplace-images-available-to-lab-creators"></a>Especificar imagens do Marketplace disponíveis para os criadores de laboratório
Como proprietário da conta de laboratório, você pode especificar as imagens do Marketplace que os criadores do laboratório podem usar para criar laboratórios na conta de laboratório. 

## <a name="select-images-available-for-labs"></a>Selecione imagens disponíveis para laboratórios
Selecione **Imagens do Marketplace** no menu esquerdo. Por padrão, você deve ver a lista completa de imagens (habilitadas e desabilitadas). Você pode filtrar a lista para ver apenas imagens ativadas/desativadas selecionando a única/opção **Ativada****desativada** na lista de baixa na parte superior. 
    
![Página Imagens do Marketplace](../media/tutorial-setup-lab-account/marketplace-images-page.png)

As imagens do Marketplace exibidas na lista são apenas aquelas que atendem as seguintes condições:
    
- Cria uma única VM
- Usa o Azure Resource Manager para provisionar VMs
- Não requer a compra de um plano de licenciamento extra

## <a name="disable-images-for-a-lab"></a>Desativar imagens para um laboratório 
Para desativar uma única imagem para um laboratório, selecione **... (elipse)** na última coluna e **selecione Desativar imagem**. 

![Desabilitar uma imagem](../media/tutorial-setup-lab-account/disable-one-image.png) 

Alternativamente, selecione a caixa de seleção antes do nome da imagem e **selecione Desativar imagens selecionadas** na barra de ferramentas. 

Para desativar várias imagens ao mesmo tempo, selecione caixas de seleção antes dos nomes das imagens e **selecione Desativar imagens selecionadas** na barra de ferramentas. 

![Desabilitar várias imagens](../media/tutorial-setup-lab-account/disable-multiple-images.png) 


## <a name="enable-images-for-a-lab"></a>Habilite imagens para um laboratório
Para habilitar uma imagem desativada, selecione **... (elipse)** na última coluna e selecione **Ativar imagem**. Alternativamente, selecione a caixa de seleção antes do nome da imagem e **selecione Habilitar imagens selecionadas** na barra de ferramentas. 

Para desativar várias imagens ao mesmo tempo, selecione caixas de seleção antes dos nomes das imagens e **selecione Ativar imagens selecionadas** na barra de ferramentas. 

## <a name="next-steps"></a>Próximas etapas
Veja os artigos a seguir:

- [Como um proprietário de laboratório, crie e gerencie laboratórios](how-to-manage-classroom-labs.md)
- [Como um proprietário de laboratório, configure e publique modelos](how-to-create-manage-template.md)
- [Como um proprietário de laboratório, configure e controle o uso de um laboratório](how-to-configure-student-usage.md)
- [Como um usuário de laboratório, acesse os laboratórios de sala de aula](how-to-use-classroom-lab.md)
