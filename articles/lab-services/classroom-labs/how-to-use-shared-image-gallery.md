---
title: Usar uma galeria de imagens compartilhadas no Azure Lab Services | Microsoft Docs
description: Saiba como configurar uma conta de laboratório para usar uma galeria de imagens compartilhadas para que um usuário possa compartilhar uma imagem com outra e outro usuário possa usar a imagem para criar uma VM de modelo no laboratório.
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
ms.openlocfilehash: 294a270107033590e340db49b85e8a67acbbb701
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83116859"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>Usar uma galeria de imagens compartilhadas no Azure Lab Services
Este artigo mostra como os professores/administrador de laboratório podem salvar uma imagem de máquina virtual de modelo em uma [Galeria de imagens compartilhada](../../virtual-machines/windows/shared-image-galleries.md) para que ela possa ser usada por outras pessoas para criar laboratórios. 

## <a name="scenarios"></a>Cenários
Estes são os dois cenários com suporte neste recurso: 

- Um administrador de conta de laboratório anexa uma galeria de imagens compartilhada à conta de laboratório e carrega uma imagem na Galeria de imagens compartilhadas fora do contexto de um laboratório. Em seguida, os criadores de laboratório podem usar essa imagem da Galeria de imagens compartilhadas para criar laboratórios. 
- Um administrador de conta de laboratório anexa uma galeria de imagens compartilhada à conta do laboratório. Um criador de laboratório (instrutor) salva a imagem personalizada de seu laboratório na Galeria de imagens compartilhadas. Em seguida, outros criadores de laboratório podem selecionar essa imagem na Galeria de imagens compartilhadas para criar um modelo para seus laboratórios. 

    Quando uma imagem é salva em uma galeria de imagens compartilhada, Azure Lab Services Replica a imagem salva em outras regiões disponíveis na mesma [geografia](https://azure.microsoft.com/global-infrastructure/geographies/). Ele garante que a imagem esteja disponível para laboratórios criados em outras regiões na mesma geografia. Salvar imagens em uma galeria de imagens compartilhadas gera um custo adicional, o que inclui o custo de todas as imagens replicadas. Esse custo é separado do custo de uso de Azure Lab Services. Para obter mais informações sobre preços da Galeria de imagens compartilhadas, consulte [Galeria de imagens compartilhadas – cobrança]( https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#billing).

    > [!NOTE]
    > Azure Lab Services dá suporte à criação de VMs de modelo com base em imagens **generalizadas** e **especializadas** em uma galeria de imagens compartilhada. 


## <a name="prerequisites"></a>Pré-requisitos
- Crie uma galeria de imagens compartilhada usando [Azure PowerShell](../../virtual-machines/windows/shared-images.md) ou [CLI do Azure](../../virtual-machines/linux/shared-images.md).
- Você anexou a Galeria de imagens compartilhadas à conta do laboratório. Para obter as instruções passo a passo, consulte [como anexar ou desanexar a Galeria de imagens compartilhadas](how-to-attach-detach-shared-image-gallery.md).


## <a name="save-an-image-to-the-shared-image-gallery"></a>Salvar uma imagem na Galeria de imagens compartilhadas
Depois que uma galeria de imagens compartilhadas é anexada, um administrador de conta de laboratório ou um professor pode salvar uma imagem na Galeria de imagens compartilhadas para que possa ser reutilizada por outros professores. 

1. Na página **modelo** do laboratório, selecione **exportar para a Galeria de imagens compartilhadas** na barra de ferramentas.

    ![Botão Salvar imagem](../media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-button.png)
2. Na caixa de diálogo **exportar para a Galeria de imagens compartilhadas** , insira um **nome para a imagem**e, em seguida, selecione **Exportar**. 

    ![Caixa de diálogo Exportar para Galeria de imagens compartilhadas](../media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-dialog.png)
3. Você pode ver o progresso dessa operação na página **modelo** . Esta operação pode levar algum tempo. 

    ![Exportação em andamento](../media/how-to-use-shared-image-gallery/exporting-image-in-progress.png)
4. Quando a operação de exportação for bem-sucedida, você verá a seguinte mensagem:

    ![Exportação concluída](../media/how-to-use-shared-image-gallery/exporting-image-completed.png)

    > [!NOTE]
    > Depois de salvar a imagem na Galeria de imagens compartilhadas, você poderá usá-la da galeria ao criar outro laboratório. 
    > 
    > Você também pode carregar uma imagem na Galeria de imagens compartilhadas fora do contexto de um laboratório. Para obter mais informações, consulte [visão geral da Galeria de imagens compartilhadas](../../virtual-machines/windows/shared-images.md). 

    

## <a name="use-an-image-from-the-shared-image-gallery"></a>Usar uma imagem da Galeria de imagens compartilhadas
Um professor/professor pode escolher uma imagem personalizada disponível na Galeria de imagens compartilhadas para o modelo durante a criação do novo laboratório.

![Usar a imagem de máquina virtual da Galeria](../media/how-to-use-shared-image-gallery/use-shared-image.png)

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre galerias de imagens compartilhadas, consulte [Galeria de imagens compartilhadas](../../virtual-machines/windows/shared-image-galleries.md).
