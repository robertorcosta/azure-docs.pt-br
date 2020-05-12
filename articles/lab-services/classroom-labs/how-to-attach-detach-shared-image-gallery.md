---
title: Anexar ou desanexar uma galeria de imagens compartilhadas no Azure Lab Services | Microsoft Docs
description: Este artigo descreve como anexar uma galeria de imagens compartilhada a um laboratório de sala de aula no Azure Lab Services.
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
ms.date: 05/08/2020
ms.author: spelluru
ms.openlocfilehash: ce79674462f82e05cc07e9e470cb82ff8e47f672
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83118474"
---
# <a name="attach-or-detach-a-shared-image-gallery-in-azure-lab-services"></a>Anexar ou desanexar uma galeria de imagens compartilhadas no Azure Lab Services
Este artigo mostra como anexar ou desanexar uma galeria de imagens compartilhadas a uma conta de laboratório. 

## <a name="scenarios"></a>Cenários
Estes são os dois cenários com suporte neste recurso: 

- Um administrador de conta de laboratório anexa uma galeria de imagens compartilhada à conta de laboratório e carrega uma imagem na Galeria de imagens compartilhadas fora do contexto de um laboratório. Em seguida, os criadores de laboratório podem usar essa imagem da Galeria de imagens compartilhadas para criar laboratórios. 
- Um administrador de conta de laboratório anexa uma galeria de imagens compartilhada à conta do laboratório. Um criador de laboratório (instrutor) salva a imagem personalizada de seu laboratório na Galeria de imagens compartilhadas. Em seguida, outros criadores de laboratório podem selecionar essa imagem na Galeria de imagens compartilhadas para criar um modelo para seus laboratórios. 

    Quando uma imagem é salva em uma galeria de imagens compartilhada, Azure Lab Services Replica a imagem salva em outras regiões disponíveis na mesma [geografia](https://azure.microsoft.com/global-infrastructure/geographies/). Ele garante que a imagem esteja disponível para laboratórios criados em outras regiões na mesma geografia. Salvar imagens em uma galeria de imagens compartilhadas gera um custo adicional, o que inclui o custo de todas as imagens replicadas. Esse custo é separado do custo de uso de Azure Lab Services. Para obter mais informações sobre preços da Galeria de imagens compartilhadas, consulte [Galeria de imagens compartilhadas – cobrança]( https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#billing).

    > [!NOTE]
    > Azure Lab Services dá suporte à criação de VMs de modelo com base em imagens **generalizadas** e **especializadas** em uma galeria de imagens compartilhada. 


## <a name="configure-at-the-time-of-lab-account-creation"></a>Configurar no momento da criação da conta do laboratório
Ao criar uma conta de laboratório, você pode anexar uma galeria de imagens compartilhada à conta do laboratório. Você pode selecionar uma galeria de imagens compartilhada existente na lista suspensa ou criar uma nova. Para criar e anexar uma galeria de imagens compartilhada à conta do laboratório, selecione **criar nova**, insira um nome para a galeria e digite **OK**. 

![Configurar a Galeria de imagens compartilhadas no momento da criação da conta do laboratório](../media/how-to-use-shared-image-gallery/new-lab-account.png)

## <a name="configure-after-the-lab-account-is-created"></a>Configurar após a criação da conta de laboratório
Depois que a conta do laboratório for criada, você poderá executar as seguintes tarefas:

- Criar e anexar uma galeria de imagens compartilhadas
- Anexar uma galeria de imagens compartilhada à conta do laboratório
- Desanexar uma galeria de imagens compartilhada da conta do laboratório

## <a name="create-and-attach-a-shared-image-gallery"></a>Criar e anexar uma galeria de imagens compartilhadas
1. Entre no [portal do Azure](https://portal.azure.com).
2. Selecione **Todos os serviços** no menu esquerdo. Selecione **Serviços de Laboratório** na seção **DEVOPS**. Se você selecionar a estrela (`*`) ao lado de **Serviços de Laboratório**, ela será adicionada à seção **FAVORITOS** no menu esquerdo. Na próxima vez em diante, selecione **Serviços de Laboratório** em **FAVORITOS**.

    ![Todos os Serviços -> Serviços de Laboratório](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Selecione sua conta de laboratório para ver a página da **conta do laboratório** . 
4. Selecione **Galeria de imagens compartilhadas** no menu à esquerda e selecione **+ criar** na barra de ferramentas.  

    ![Botão criar galeria de imagens compartilhadas](../media/how-to-use-shared-image-gallery/new-shared-image-gallery-button.png)
5. Na janela **criar galeria de imagens compartilhadas** , insira um **nome** para a galeria e digite **OK**. 

    ![Janela criar galeria de imagens compartilhadas](../media/how-to-use-shared-image-gallery/create-shared-image-gallery-window.png)

    Azure Lab Services cria a Galeria de imagens compartilhada e a anexa à conta do laboratório. Todos os laboratórios criados nesta conta de laboratório têm acesso à galeria de imagens compartilhada anexada. 

    ![Galeria de imagens anexadas](../media/how-to-use-shared-image-gallery/image-gallery-in-list.png)

    No painel inferior, você vê imagens na Galeria de imagens compartilhadas. Nesta nova galeria, não há imagens. Quando você carrega imagens na Galeria, elas são exibidas nesta página.     

    Todas as imagens na Galeria de imagens compartilhada anexada são habilitadas por padrão. Você pode habilitar ou desabilitar as imagens selecionadas selecionando-as na lista e usando o botão **Habilitar imagens selecionadas** ou **desabilitar imagens selecionadas** .

## <a name="attach-an-existing-shared-image-gallery"></a>Anexar uma galeria de imagens compartilhada existente
O procedimento a seguir mostra como anexar uma galeria de imagens compartilhada existente a uma conta de laboratório. 

1. Na página **conta do laboratório** , selecione **Galeria de imagens compartilhadas** no menu à esquerda e selecione **anexar** na barra de ferramentas. 

    ![Galeria de imagens compartilhadas – botão Adicionar](../media/how-to-use-shared-image-gallery/sig-attach-button.png)
5. Na página **anexar uma galeria de imagens compartilhada existente** , selecione sua galeria de imagens compartilhadas e selecione **OK**.

    ![Selecionar uma galeria existente](../media/how-to-use-shared-image-gallery/select-image-gallery.png)
6. Você verá a tela a seguir: 

    ![Minha galeria na lista](../media/how-to-use-shared-image-gallery/my-gallery-in-list.png)
    
    Neste exemplo, ainda não há imagens na Galeria de imagens compartilhadas.

    Azure Lab Services identidade é adicionada como um colaborador à galeria de imagens compartilhadas que é anexada ao laboratório. Ele permite que os professores/administradores de ti salvem imagens de máquinas virtuais na Galeria de imagens compartilhadas. Todos os laboratórios criados nesta conta de laboratório têm acesso à galeria de imagens compartilhada anexada. 

    Todas as imagens na Galeria de imagens compartilhada anexada são habilitadas por padrão. Você pode habilitar ou desabilitar as imagens selecionadas selecionando-as na lista e usando o botão **Habilitar imagens selecionadas** ou **desabilitar imagens selecionadas** . 

## <a name="detach-a-shared-image-gallery"></a>Desanexar uma galeria de imagens compartilhada
Somente uma galeria de imagens compartilhadas pode ser anexada a um laboratório. Se você quiser anexar outra galeria de imagens compartilhada, desanexe a atual antes de anexar a nova. Para desanexar uma galeria de imagens compartilhada do seu laboratório, selecione **desanexar** na barra de ferramentas e confirme a operação de desanexação. 

![Desanexar a Galeria de imagens compartilhadas da conta do laboratório](../media/how-to-use-shared-image-gallery/detach.png)

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre como salvar uma imagem de laboratório na Galeria de imagens compartilhadas ou usar uma imagem da Galeria de imagens compartilhadas para criar uma VM, consulte [como usar a Galeria de imagens compartilhadas](how-to-use-shared-image-gallery.md).

Para obter mais informações sobre galerias de imagens compartilhadas em geral, consulte [Galeria de imagens compartilhadas](../../virtual-machines/windows/shared-image-galleries.md).
