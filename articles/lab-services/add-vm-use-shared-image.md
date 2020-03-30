---
title: Adicionar uma VM usando uma imagem compartilhada no Azure DevTest Labs | Microsoft Docs
description: Saiba como adicionar uma máquina virtual (VM) usando uma imagem da galeria de imagens compartilhadas anexada no Azure DevTest Labs
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2019
ms.author: spelluru
ms.openlocfilehash: c13b2c3d00a0ab0af0f7785feae645d907ffdaa3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "68775585"
---
# <a name="add-a-vm-using-an-image-from-the-attached-shared-image-gallery"></a>Adicione uma VM usando uma imagem da galeria de imagens compartilhadas anexada
O Azure DevTest Labs permite que você conecte uma galeria de imagens compartilhadas ao seu laboratório e, em seguida, use imagens na galeria como bases para as VMs que você cria no laboratório. Para saber como anexar uma galeria de imagens compartilhadas ao seu laboratório, consulte [Configurar galeria de imagens compartilhadas](configure-shared-image-gallery.md). Este artigo mostra como adicionar uma VM ao seu laboratório usando uma imagem da galeria de imagens compartilhadas anexada como base. 

## <a name="azure-portal"></a>Portal do Azure
Nesta seção, você aprende a usar o portal Azure para adicionar uma VM ao seu laboratório com base em uma imagem da galeria de imagens compartilhadas anexada. Esta seção não fornece instruções detalhadas passo a passo para criar uma VM usando o portal Azure. Para obter esses detalhes, consulte [Criar um portal VM - Azure](devtest-lab-add-vm.md). Ele apenas destaca as etapas em que você seleciona uma imagem da galeria de imagens compartilhadas anexada e seleciona uma versão da imagem que deseja usar. 

Ao adicionar uma VM ao seu laboratório, você pode selecionar uma imagem da galeria de imagens compartilhadas anexada como uma imagem base: 

![Selecione uma imagem compartilhada para a base](./media/add-vm-use-shared-image/select-shared-image-for-base.png)

Em seguida, na guia **Configurações Avançadas** da página **Criar recurso do laboratório,** você pode selecionar a versão da imagem que deseja usar como imagem base:

![Selecione a versão da imagem](./media/add-vm-use-shared-image/select-version-shared-image.png)

Você pode mudar para usar uma versão diferente da imagem depois que a VM é criada. 

## <a name="resource-manager-template"></a>Modelo do Resource Manager
Se você estiver usando um modelo do Azure Resource Manager para criar uma máquina virtual usando uma imagem de galeria de imagens compartilhadas, especifique um valor para o **ImageId compartilhado** na seção **Propriedades.** Consulte o seguinte exemplo: 

```json
"resources": [
{
    ...
    "properties": {
         "sharedImageId": "/subscriptions/111111111-1111-1111-1111-111111111111/resourcegroups/mydtlrg/providers/microsoft.devtestlab/labs/mydtllab/sharedgalleries/spsig/sharedimages/myimagefromgallery",
        "sharedImageVersion": "1.0.1",
        ...
    }
}
],
```

Para obter um exemplo completo do exemplo do resource manager, consulte [Criar uma máquina virtual usando uma](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-vm-username-pwd-sharedimage) amostra de imagem compartilhada da galeria em nosso repositório gitHub. 

## <a name="rest-api"></a>API REST

1. Primeiro, você precisa obter o ID da imagem na galeria de imagens compartilhadas. Uma maneira é listar todas as imagens na galeria de imagens compartilhadas anexada usando o seguinte comando GET. 

    ```rest
    GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}/sharedimages?api-version= 2018-10-15-preview
    ```
2. Invoque o método PUT em máquinas virtuais passando o ID da `properties.SharedImageId`imagem compartilhada que você recebeu da chamada anterior para o .

## <a name="next-steps"></a>Próximas etapas
Para aprender como anexar uma galeria de imagens compartilhadas a um laboratório e configurá-la, consulte [Configurar galeria de imagens compartilhadas](configure-shared-image-gallery.md).