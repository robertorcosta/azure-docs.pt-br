---
title: Usar imagens de VM compartilhadas para criar um conjunto de dimensionamento no Azure
description: Saiba como usar a CLI do Azure para criar imagens de VM compartilhadas a usar para implantar Conjuntos de Dimensionamento de Máquinas Virtuais no Azure.
author: axayjo
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: akjosh
ms.reviewer: cynthn
ms.custom: ''
ms.openlocfilehash: 83b3d5c904a65b28482acf8b685c939493c8c03b
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2020
ms.locfileid: "76276279"
---
# <a name="create-and-use-shared-images-for-virtual-machine-scale-sets-with-the-azure-cli-20"></a>Criar e usar imagens personalizadas para Conjuntos de Dimensionamento de Máquinas Virtuais com a CLI do Azure 2.0

Ao criar um conjunto de dimensionamento, você especifica uma imagem a ser usada quando as instâncias de VM forem implantadas. A [Galerias de imagens compartilhadas](shared-image-galleries.md) simplifica bastante o compartilhamento da imagem personalizada em sua organização. Imagens personalizadas são como imagens do marketplace, mas você mesmo as cria. As imagens personalizadas podem ser usadas para configurações de inicialização como o pré-carregamento de aplicativos, configurações de aplicativos e outras configurações do sistema operacional. A galeria de imagens compartilhadas permite compartilhar suas imagens da VM personalizadas com outras pessoas em sua organização, dentro ou entre regiões, em um locatário do AAD. Escolha quais imagens você deseja compartilhar, em quais regiões deseja torná-las disponíveis e com quem deseja compartilhá-las. Você pode criar várias galerias, de modo que pode agrupar logicamente as imagens compartilhadas. A galeria é um recurso de nível superior que fornece RBAC (controle de acesso completo baseado em função). As imagens podem ser atualizadas, e você pode optar por replicar cada versão da imagem para um conjunto diferente de regiões do Azure. A galeria funciona apenas com imagens gerenciadas. 

>[!NOTE]
> Este artigo explica o processo de usar uma imagem gerenciada generalizada. Não há suporte para criar um conjunto de dimensionamento a partir de uma imagem de VM especializada.


[!INCLUDE [virtual-machines-common-shared-images-cli](../../includes/virtual-machines-common-shared-images-cli.md)]

## <a name="create-a-scale-set-from-the-custom-vm-image"></a>Criar um conjunto de dimensionamento a partir da imagem de VM personalizada
Crie um conjunto de dimensionamento com [`az vmss create`](/cli/azure/vmss#az-vmss-create). Em vez de uma imagem de plataforma, como *UbuntuLTS* ou *CentOS*, especifique o nome da sua imagem de VM personalizada. O exemplo a seguir cria um conjunto de dimensionamento chamado *myScaleSet* que usa a imagem personalizada denominada *myImage* da etapa anterior:

```azurecli-interactive
az vmss create \
  -g myGalleryRG \
  -n myScaleSet \
  --image "/subscriptions/<subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0" \
  --admin-username azureuser \
  --generate-ssh-keys
```

Leva alguns minutos para criar e configurar todos os recursos e as VMs do conjunto de dimensionamento.


[!INCLUDE [virtual-machines-common-gallery-list-cli](../../includes/virtual-machines-common-gallery-list-cli.md)]


## <a name="clean-up-resources"></a>Limpar os recursos
Para remover o conjunto de dimensionamento e outros recursos, exclua o grupo de recursos e todos os seus recursos com [az group delete](/cli/azure/group). O parâmetro `--no-wait` retorna o controle ao prompt sem aguardar a conclusão da operação. O parâmetro `--yes` confirma que você deseja excluir os recursos sem um prompt adicional para fazer isso.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Próximos passos

Você também pode criar um recurso de Galeria de imagens compartilhadas usando modelos. Há vários Modelos de Início Rápido do Azure disponíveis: 

- [Criar uma Galeria de Imagens Compartilhadas](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Criar uma Definição de Imagem em uma Galeria de Imagens Compartilhadas](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Criar uma Versão da Imagem em uma Galeria de Imagens Compartilhadas](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Criar uma VM por meio de uma Versão da Imagem](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)


Se você tiver algum problema, você poderá [solucionar problemas de galerias de imagens compartilhadas](troubleshooting-shared-images.md).
