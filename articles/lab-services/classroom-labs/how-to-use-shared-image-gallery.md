---
title: Use uma galeria de imagens compartilhadas no Azure Lab Services | Microsoft Docs
description: Saiba como configurar uma conta de laboratório para usar uma galeria de imagens compartilhadas para que um usuário possa compartilhar uma imagem com outra e outro usuário possa usar a imagem para criar uma VM modelo no laboratório.
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
ms.date: 05/15/2020
ms.author: spelluru
ms.openlocfilehash: 8253b784a427ceb3842b6b4d585c52a2c30e0c4d
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592416"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>Usar uma galeria de imagens compartilhadas no Azure Lab Services
Este artigo mostra como educadores/administradores de laboratório podem salvar um modelo de imagem de máquina virtual em uma [galeria de imagens compartilhada](../../virtual-machines/windows/shared-image-galleries.md) para que ela possa ser usada por outras pessoas para criar laboratórios. 

## <a name="scenarios"></a>Cenários
Aqui estão alguns dos cenários aos quais esse recurso dá suporte: 

- Um administrador de conta de laboratório anexa uma galeria de imagens compartilhadas à conta de laboratório e carrega uma imagem para a galeria de imagens compartilhadas fora do contexto de um laboratório. Em seguida, os criadores de laboratório podem usar essa imagem da galeria de imagens compartilhadas para criar laboratórios. 
- Um administrador de conta de laboratório anexa uma galeria de imagens compartilhadas à conta de laboratório. Um criador de laboratório (instrutor) salva a imagem personalizada do laboratório na galeria de imagens compartilhadas. Em seguida, outros criadores de laboratório podem selecionar essa imagem na galeria de imagens compartilhadas para criar um modelo para os laboratórios deles. 

    Quando uma imagem é salva em uma galeria de imagens compartilhadas, o Azure Lab Services replica a imagem salva em outras regiões disponíveis na mesma [geografia](https://azure.microsoft.com/global-infrastructure/geographies/). Isso verifica se a imagem está disponível para laboratórios criados em outras regiões na mesma geografia. Salvar imagens em uma galeria de imagens compartilhadas gera um custo adicional, que inclui o custo de todas as imagens replicadas. Esse custo é separado do custo de uso do Azure Lab Services. Para obter mais informações sobre o preço da Galeria de Imagens Compartilhadas, confira [Galeria de Imagens Compartilhadas – cobrança]( https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#billing).

    > [!NOTE]
    > O Azure Lab Services dá suporte à criação de VMs modelo com base em imagens **generalizadas** e **especializadas** em uma galeria de imagens compartilhadas. 


## <a name="prerequisites"></a>Pré-requisitos
- Crie uma galeria de imagens compartilhada usando o [Azure PowerShell](../../virtual-machines/windows/shared-images.md) ou o [CLI do Azure](../../virtual-machines/linux/shared-images.md).
- Você anexou uma galeria de imagens compartilhadas à conta de laboratório. Para obter instruções passo a passo, consulte [Como anexar ou desanexar a galeria de imagens compartilhadas](how-to-attach-detach-shared-image-gallery.md).


## <a name="save-an-image-to-the-shared-image-gallery"></a>Salvar uma imagem na galeria de imagens compartilhadas
Depois que uma galeria de imagens compartilhadas é anexada, um administrador de conta de laboratório ou um educador pode salvar uma imagem na galeria de imagens compartilhadas para que ela possa ser reutilizada por outros educadores. 

1. Na página **Modelo** do laboratório, selecione **Exportar para a Galeria de Imagens Compartilhadas** na barra de ferramentas.

    ![Botão Salvar Imagem](../media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-button.png)
2. Na caixa de diálogo **Exportar para a Galeria de Imagens Compartilhadas**, insira um **nome para a imagem**e, em seguida, selecione **Exportar**. 

    ![Caixa de diálogo Exportar para Galeria de Imagens Compartilhadas](../media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-dialog.png)
3. Você pode ver o progresso dessa operação na página do **Modelo**. Esta operação pode levar algum tempo. 

    ![Exportação em andamento](../media/how-to-use-shared-image-gallery/exporting-image-in-progress.png)
4. Quando a operação de exportação for bem-sucedida, você verá a seguinte mensagem:

    ![Exportação concluída](../media/how-to-use-shared-image-gallery/exporting-image-completed.png)

    > [!NOTE]
    > Depois de salvar a imagem na galeria de imagens compartilhadas, você poderá usar essa imagem na galeria para criar outro laboratório. 
    > 
    > Você também pode carregar uma imagem na galeria de imagens compartilhadas fora do contexto de um laboratório. Para obter mais informações, consulte [Visão geral da galeria de imagens compartilhadas](../../virtual-machines/windows/shared-images.md). 

    

## <a name="use-an-image-from-the-shared-image-gallery"></a>Salvar uma imagem da galeria de imagens compartilhadas
Um educador pode escolher uma imagem personalizada disponível na galeria de imagens compartilhadas para usar como modelo durante a criação do novo laboratório.

![Use uma imagem da máquina virtual na galeria](../media/how-to-use-shared-image-gallery/use-shared-image.png)

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre galerias de imagens compartilhadas, consulte [galeria de imagens compartilhadas](../../virtual-machines/windows/shared-image-galleries.md).
