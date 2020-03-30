---
title: Anexar ou desvincular uma galeria de imagens compartilhadas no Azure Lab Services | Microsoft Docs
description: Este artigo descreve como anexar uma galeria de imagens compartilhadas a um laboratório de sala de aula no Azure Lab Services.
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
ms.date: 02/24/2020
ms.author: spelluru
ms.openlocfilehash: 00dbef7b4453ffcb54020340bde51f55827759a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284311"
---
# <a name="attach-or-detach-a-shared-image-gallery-in-azure-lab-services"></a>Anexar ou desvincular uma galeria de imagens compartilhadas no Azure Lab Services
Professores/administradores de laboratório podem salvar uma imagem VM modelo em uma [galeria de imagens compartilhadas do](../../virtual-machines/windows/shared-image-galleries.md) Azure para que ela seja reutilizada por outros. Como primeiro passo, o administração do laboratório anexa uma galeria de imagens compartilhadas existente à conta do laboratório. Uma vez que a galeria de imagens compartilhadas é anexada, os laboratórios criados na conta do laboratório podem salvar imagens na galeria de imagens compartilhadas. Outros professores podem selecionar essa imagem na galeria de imagens compartilhadas para criar um modelo para suas aulas. 

Quando uma imagem é salva em uma galeria de imagens compartilhadas, o Azure Lab Services replica a imagem salva para outras regiões disponíveis na mesma [geografia](https://azure.microsoft.com/global-infrastructure/geographies/). Ele garante que a imagem esteja disponível para laboratórios criados em outras regiões da mesma geografia. Salvar imagens em uma galeria de imagens compartilhadas incorre em um custo adicional, que inclui o custo para todas as imagens replicadas. Esse custo é separado do custo de uso do Azure Lab Services. Para obter mais informações sobre os preços da Galeria de Imagens Compartilhadas, consulte [A Galeria de Imagens Compartilhadas – Faturamento]( https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#billing).

Este artigo mostra como anexar ou desvincular uma galeria de imagens compartilhadas a uma conta de laboratório. 

> [!NOTE]
> Atualmente, o Azure Lab Services suporta a criação de VMs modelo com base apenas em imagens **VM generalizadas** (imagens não especializadas) em uma galeria de imagens compartilhadas. 


## <a name="configure-at-the-time-of-lab-account-creation"></a>Configurar no momento da criação da conta de laboratório
Quando você está criando uma conta de laboratório, você pode anexar uma galeria de imagens compartilhadas à conta do laboratório. Você pode selecionar uma galeria de imagens compartilhadas existente na lista de paradas ou criar uma nova. Para criar e anexar uma galeria de imagens compartilhadas à conta de laboratório, selecione **Criar novo,** digite um nome para a galeria e digite **OK**. 

![Configure galeria de imagens compartilhadas no momento da criação da conta de laboratório](../media/how-to-use-shared-image-gallery/new-lab-account.png)

## <a name="configure-after-the-lab-account-is-created"></a>Configure-se depois que a conta de laboratório for criada
Depois que a conta do laboratório é criada, você pode fazer as seguintes tarefas:

- Criar e anexar uma galeria de imagens compartilhadas
- Anexar uma galeria de imagens compartilhadas à conta do laboratório
- Desconecte uma galeria de imagens compartilhadas da conta do laboratório

## <a name="create-and-attach-a-shared-image-gallery"></a>Criar e anexar uma galeria de imagens compartilhadas
1. Faça login no [portal Azure](https://portal.azure.com).
2. Selecione **Todos os serviços** no menu esquerdo. Selecione **Serviços de Laboratório** na seção **DEVOPS**. Se você selecionar a estrela (`*`) ao lado de **Serviços de Laboratório**, ela será adicionada à seção **FAVORITOS** no menu esquerdo. Na próxima vez em diante, selecione **Serviços de Laboratório** em **FAVORITOS**.

    ![Todos os Serviços -> Serviços de Laboratório](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Selecione sua conta de laboratório para ver a página de Conta do **Laboratório.** 
4. Selecione **Galeria de imagens compartilhadas** no menu esquerdo e selecione **+ Criar** na barra de ferramentas.  

    ![Criar botão de galeria de imagens compartilhadas](../media/how-to-use-shared-image-gallery/new-shared-image-gallery-button.png)
5. Na janela **Criar galeria de imagens compartilhadas,** digite um **nome** para a galeria e digite **OK**. 

    ![Criar janela de galeria de imagens compartilhadas](../media/how-to-use-shared-image-gallery/create-shared-image-gallery-window.png)

    O Azure Lab Services cria a galeria de imagens compartilhadas e a anexa à conta do laboratório. Todos os laboratórios criados nesta conta de laboratório têm acesso à galeria de imagens compartilhadas anexada. 

    ![Galeria de imagens anexada](../media/how-to-use-shared-image-gallery/image-gallery-in-list.png)

    No painel inferior, você vê imagens na galeria de imagens compartilhadas. Nesta nova galeria, não há imagens. Quando você envia imagens para a galeria, você as vê nesta página.     

    Todas as imagens na galeria de imagens compartilhadas anexadas são habilitadas por padrão. Você pode ativar ou desativar imagens selecionadas selecionando-as na lista e usando o botão **Ativar imagens selecionadas** ou Desativar o botão **Imagens selecionadas.**

## <a name="attach-an-existing-shared-image-gallery"></a>Anexar uma galeria de imagens compartilhadas existente
O procedimento a seguir mostra como anexar uma galeria de imagens compartilhadas existente a uma conta de laboratório. 

1. Na página Conta do **laboratório,** selecione **Galeria de imagens compartilhadas** no menu esquerdo e **selecione Anexar** na barra de ferramentas. 

    ![Galeria de imagens compartilhadas - Adicionar botão](../media/how-to-use-shared-image-gallery/sig-attach-button.png)
5. Na **anexação de uma página existente da Galeria de Imagens Compartilhadas,** selecione sua galeria de imagens compartilhadas e selecione **OK**.

    ![Selecione uma galeria existente](../media/how-to-use-shared-image-gallery/select-image-gallery.png)
6. Você vê a seguinte tela: 

    ![Minha galeria na lista](../media/how-to-use-shared-image-gallery/my-gallery-in-list.png)
    
    Neste exemplo, ainda não há imagens na galeria de imagens compartilhadas.

    A identidade do Azure Lab Services é adicionada como um contribuinte para a galeria de imagens compartilhadas que é anexada ao laboratório. Ele permite que professores/admin de TI salvem imagens de máquinas virtuais na galeria de imagens compartilhadas. Todos os laboratórios criados nesta conta de laboratório têm acesso à galeria de imagens compartilhadas anexada. 

    Todas as imagens na galeria de imagens compartilhadas anexadas são habilitadas por padrão. Você pode ativar ou desativar imagens selecionadas selecionando-as na lista e usando o botão **Ativar imagens selecionadas** ou Desativar o botão **Imagens selecionadas.** 

## <a name="detach-a-shared-image-gallery"></a>Desconecte uma galeria de imagens compartilhadas
Apenas uma galeria de imagens compartilhadas pode ser anexada a um laboratório. Se você quiser anexar outra galeria de imagens compartilhadas, desconecte a atual antes de anexar a nova. Para separar uma galeria de imagens compartilhadas do seu laboratório, **selecione Desapegar** na barra de ferramentas e confirme a operação de desapego. 

![Desconecte a galeria de imagens compartilhadas da conta do laboratório](../media/how-to-use-shared-image-gallery/detach.png)

## <a name="next-steps"></a>Próximas etapas
Para saber como salvar uma imagem de laboratório na galeria de imagens compartilhadas ou usar uma imagem da galeria de imagens compartilhadas para criar uma VM, veja [Como usar a galeria de imagens compartilhadas](how-to-use-shared-image-gallery.md).

Para obter mais informações sobre galerias de imagens compartilhadas em geral, consulte [galeria de imagens compartilhadas](../../virtual-machines/windows/shared-image-galleries.md).
