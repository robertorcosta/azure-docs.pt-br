---
title: Configure uma galeria de imagens compartilhadas no Azure DevTest Labs | Microsoft Docs
description: Saiba como configurar uma galeria de imagens compartilhadas no Azure DevTest Labs
services: devtest-lab
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
ms.date: 09/05/2019
ms.author: spelluru
ms.openlocfilehash: 9593d60f76802cd515ca85616bce028cf3aa0d49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77589310"
---
# <a name="configure-a-shared-image-gallery-in-azure-devtest-labs"></a>Configurar uma galeria de imagem compartilhada no Azure DevTest Labs
O DevTest Labs agora suporta o recurso [DevTest Gallery.](../virtual-machines/windows/shared-image-galleries.md) Ele permite que os usuários de laboratório acessem imagens de um local compartilhado enquanto criam recursos de laboratório. Ele também ajuda você a construir estrutura e organização em torno de suas imagens VM gerenciadas medida. O recurso da Galeria de Imagens Compartilhadas suporta:

- Replicação global gerenciada de imagens
- Versão e agrupamento de imagens para facilitar o gerenciamento
- Torne suas imagens altamente disponíveis com contas ZRS (Zone Redundant Storage, armazenamento redundante de zona) em regiões que suportam zonas de disponibilidade. A ZRS oferece melhor resiliência contra falhas zonais.
- Compartilhando entre assinaturas e até mesmo entre inquilinos, usando o RBAC (Role-Based Access Control).

Para obter mais informações, consulte [a documentação da Galeria de Imagens Compartilhadas](../virtual-machines/windows/shared-image-galleries.md). 
 
Se você tiver um grande número de imagens gerenciadas que precise manter e gostaria de disponibilizá-los em toda a empresa, poderá usar uma galeria de imagem compartilhada como um repositório que facilite a atualização e o compartilhamento das suas imagens. Como dono de laboratório, você pode anexar uma galeria de imagens compartilhadas existente ao seu laboratório. Uma vez que esta galeria é anexada, os usuários de laboratório podem criar máquinas a partir dessas imagens mais recentes. Um dos principais benefícios desse recurso é que o DevTest Labs agora pode tirar vantagem de compartilhar imagens em laboratórios, em assinaturas e em todas as regiões. 

> [!NOTE]
> Para saber mais sobre os custos associados ao serviço de Galeria de Imagens Compartilhadas, consulte [Billing for Shared Image Gallery](../virtual-machines/windows/shared-image-galleries.md#billing).

## <a name="considerations"></a>Considerações
- Você só pode anexar uma galeria de imagens compartilhadas a um laboratório de cada vez. Se você quiser anexar outra galeria, você precisará separar a existente e anexar outra. 
- Atualmente, o DevTest Labs suporta imagens generalizadas da galeria de imagens compartilhadas.
- Atualmente, a DevTest Labs não suporta o upload de imagens para a galeria através do laboratório. 
- Ao criar uma máquina virtual usando uma imagem compartilhada da galeria de imagens, o DevTest Labs sempre usa a versão mais recente publicada desta imagem. No entanto, se uma imagem tiver várias versões, o usuário pode optar por criar uma máquina a partir de uma versão anterior, indo para a guia configurações Avançadas durante a criação da máquina virtual.  
- Embora o DevTest Labs faça automaticamente uma melhor tentativa de garantir que a galeria de imagens compartilhadas replique imagens para a região em que o Laboratório existe, nem sempre é possível. Para evitar que os usuários tenham problemas para criar VMs a partir dessas imagens, certifique-se de que as imagens já estão replicadas na região do laboratório."

## <a name="use-azure-portal"></a>Usar o portal do Azure
1. Faça login no [portal Azure](https://portal.azure.com).
1. Selecione **Todos os serviços** no menu de navegação à esquerda.
1. Selecione **DevTest Labs** da lista.
1. Na lista de laboratórios, selecione seu **laboratório.**
1. Selecione **Configuração e políticas** na seção **Configurações** no menu esquerdo.
1. Selecione **Galerias de imagens compartilhadas** em **bases de máquinas virtuais** no menu esquerdo.

    ![Menu Galerias de Imagens Compartilhadas](./media/configure-shared-image-gallery/shared-image-galleries-menu.png)
1. Conecte uma galeria de imagens compartilhadas existente ao seu laboratório clicando no botão **Anexar** e selecionando sua galeria no dropdown.

    ![Anexar](./media/configure-shared-image-gallery/attach-options.png)
1. Vá para a galeria anexada e configure sua galeria para **ativar ou desativar** imagens compartilhadas para criação de VM. Selecione uma galeria de imagens da lista para configurá-la. 

    Por padrão, **permitir que todas as imagens sejam usadas como bases de máquinas virtuais** é definida como **Sim**. Isso significa que todas as imagens disponíveis na galeria de imagens compartilhadas anexadas estarão disponíveis para um usuário de laboratório ao criar um novo laboratório VM. Se o acesso a determinadas imagens precisar ser restrito, **altere Permitir que todas as imagens sejam usadas como bases de máquinas virtuais** para **Não,** e selecione as imagens que deseja permitir ao criar VMs e selecione o botão **Salvar.**

    ![Habilitar ou desativar](./media/configure-shared-image-gallery/enable-disable.png)
1. Os usuários do laboratório podem então criar uma máquina virtual usando as imagens habilitadas clicando em **+Adicionar** e encontrar a imagem na **página base.**

    ![Usuários de laboratório](./media/configure-shared-image-gallery/lab-users.png)
## <a name="use-azure-resource-manager-template"></a>Usar o modelo do Azure Resource Manager

### <a name="attach-a-shared-image-gallery-to-your-lab"></a>Anexar uma galeria de imagens compartilhadas ao seu laboratório
Se você estiver usando um modelo do Azure Resource Manager para anexar uma galeria de imagens compartilhadas ao seu laboratório, você precisa adicioná-la na seção de recursos do modelo do Gerenciador de recursos, conforme mostrado no exemplo a seguir:

```json
"resources": [
{
    "apiVersion": "2018-10-15-preview",
    "type": "Microsoft.DevTestLab/labs",
    "name": "mylab",
    "location": "eastus",
    "resources": [
    {
        "apiVersion":"2018-10-15-preview",
        "name":"myGallery",
        "type":"sharedGalleries",
        "properties": {
            "galleryId":"/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/mySharedGalleryRg/providers/Microsoft.Compute/galleries/mySharedGallery",
            "allowAllImages": "Enabled"
        }
    }
    ]
}
```

Para obter um exemplo completo do modelo do Gerenciador de recursos, consulte essas amostras de modelo do Gerenciador de recursos em nosso repositório público do GitHub: [Configure uma galeria de imagens compartilhadas enquanto cria um laboratório](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-lab-shared-gallery-configured).

## <a name="use-api"></a>Use API

### <a name="shared-image-galleries---create-or-update"></a>Galerias de imagens compartilhadas - criar ou atualizar

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}?api-version= 2018-10-15-preview
Body: 
{
    "properties":{
        "galleryId": "[Shared Image Gallery resource Id]",
        "allowAllImages": "Enabled"
    }
}

```

### <a name="shared-image-galleries-images---list"></a>Imagens compartilhadas de galerias de imagens - Lista 

```rest
GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}/sharedimages?api-version= 2018-10-15-preview
```




## <a name="next-steps"></a>Próximas etapas
Veja os artigos a seguir sobre a criação de uma VM usando uma imagem da galeria de imagens compartilhadas anexada: [Crie uma VM usando uma imagem compartilhada da galeria](add-vm-use-shared-image.md)
