---
title: Use uma galeria de imagens compartilhadas no Azure Lab Services | Microsoft Docs
description: Aprenda a configurar uma conta de laboratório para usar uma galeria de imagens compartilhadas para que um usuário possa compartilhar uma imagem com outro e outro usuário possa usar a imagem para criar um modelo VM no laboratório.
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
ms.openlocfilehash: c611ecdb5a2534f7368e533e3e19e6e3f96de57f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190442"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>Use uma galeria de imagens compartilhadas no Azure Lab Services
Este artigo mostra como professores/admin de laboratório podem salvar uma imagem de máquina virtual de modelo para que ela seja reutilizada por outros. Essas imagens são salvas em uma [galeria de imagens compartilhadas do](../../virtual-machines/windows/shared-image-galleries.md)Azure. Como primeiro passo, o administração do laboratório anexa uma galeria de imagens compartilhadas existente à conta do laboratório. Uma vez que a galeria de imagens compartilhadas é anexada, os laboratórios criados na conta do laboratório podem salvar imagens na galeria de imagens compartilhadas. Outros professores podem selecionar essa imagem na galeria de imagens compartilhadas para criar um modelo para suas aulas. 

> [!NOTE]
> Atualmente, o Azure Lab Services suporta a criação de VMs modelo com base apenas em imagens **VM generalizadas** (imagens não especializadas) em uma galeria de imagens compartilhadas. 

## <a name="prerequisites"></a>Pré-requisitos
- Crie uma galeria de imagens compartilhadas usando [o Azure PowerShell](../../virtual-machines/windows/shared-images.md) ou [o Azure CLI](../../virtual-machines/linux/shared-images.md).
- Você anexou a galeria de imagens compartilhadas à conta do laboratório. Para obter instruções passo a passo, consulte [Como anexar ou desprender a galeria de imagens compartilhadas](how-to-attach-detach-shared-image-gallery.md).


## <a name="save-an-image-to-the-shared-image-gallery"></a>Salve uma imagem na galeria de imagens compartilhadas
Depois que uma galeria de imagens compartilhadaé, um admin de conta de laboratório ou um professor pode salvar uma imagem na galeria de imagens compartilhadas para que ela possa ser reutilizada por outros professores. 

1. Na página **Modelo** para o laboratório, **selecione Exportar para Galeria de Imagens Compartilhadas** na barra de ferramentas.

    ![Salvar botão de imagem](../media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-button.png)
2. Na **caixa de diálogo Exportar para Galeria de Imagens Compartilhadas,** digite um **nome para a imagem**e, em seguida, selecione **Exportar**. 

    ![Exportar para a galeria de imagens compartilhadas](../media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-dialog.png)
3. Você pode ver o progresso desta operação na página **Modelo.** Esta operação pode levar algum tempo. 

    ![Exportação em andamento](../media/how-to-use-shared-image-gallery/exporting-image-in-progress.png)
4. Quando a operação de exportação é bem sucedida, você verá a seguinte mensagem:

    ![Exportação concluída](../media/how-to-use-shared-image-gallery/exporting-image-completed.png)

Você também pode enviar uma imagem para a galeria de imagens compartilhadas fora do contexto de um laboratório. Para obter mais informações, consulte [visão geral da galeria de imagens compartilhadas](../../virtual-machines/windows/shared-images.md). 

## <a name="use-an-image-from-the-shared-image-gallery"></a>Use uma imagem da galeria de imagens compartilhadas
Um professor/professor pode escolher uma imagem personalizada disponível na galeria de imagens compartilhadas para o modelo durante a criação do novo laboratório.

![Use imagem de máquina virtual da galeria](../media/how-to-use-shared-image-gallery/use-shared-image.png)

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre galerias de imagens compartilhadas, consulte [galeria de imagens compartilhadas](../../virtual-machines/windows/shared-image-galleries.md).
