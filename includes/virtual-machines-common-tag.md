---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: f6bd574c83d309ce6d6f54fdb1c7d23cb713420d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2020
ms.locfileid: "73182168"
---
## <a name="tagging-a-virtual-machine-through-templates"></a>Marcando uma máquina virtual por meio de modelos
Primeiramente, vamos observar uma marcação por meio de modelos. [Este modelo](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags) coloca marcas nos seguintes recursos: Computação (Máquina Virtual), Armazenamento (Conta de Armazenamento) e Rede (Endereço IP Público, Rede Virtual e Interface de Rede). Esse modelo destina-se a uma VM do Windows, mas pode ser adaptada para VMs do Linux.

Clique no botão **Implantar no Azure** no [link do modelo](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags). Você será direcionado para o [Portal do Azure](https://portal.azure.com/) , onde poderá implantar esse modelo.

![Implantação simples com marcas](./media/virtual-machines-common-tag/deploy-to-azure-tags.png)

Esse modelo inclui as seguintes marcas: *Departamento*, *Aplicativo* e *Criado por*. Você pode adicionar/editar essas marcas diretamente no modelo se desejar diferentes nomes de marca.

![Marcas do Azure em um modelo](./media/virtual-machines-common-tag/azure-tags-in-a-template.png)

Como pode ser visto, as marcas são pares de chave/valor definidos, separados por dois-pontos (:). As marcas devem ser definidas neste formato:

        "tags": {
            "Key1" : "Value1",
            "Key2" : "Value2"
        }

Salve o arquivo de modelo quando terminar de editá-lo com as marcas de sua escolha.

Em seguida, na seção **Editar Parâmetros** , você pode preencher os valores de suas marcas.

![Editar marcas no Portal do Azure](./media/virtual-machines-common-tag/edit-tags-in-azure-portal.png)

Clique em **Criar** para implantar esse modelo com seus valores de marca.

## <a name="tagging-through-the-portal"></a>Marcando por meio do portal
Depois de criar seus recursos com marcas, você pode exibir, adicionar e excluir as marcas no portal.

Selecione o ícone de marcas para exibir suas marcas:

![Ícone de marcas no Portal do Azure](./media/virtual-machines-common-tag/azure-portal-tags-icon.png)

Adicione uma nova marca por meio do portal definindo seu próprio par de chave/valor e salve-a.

![Adicionar nova marca no Portal do Azure](./media/virtual-machines-common-tag/azure-portal-add-new-tag.png)

Agora, a nova marca deve aparecer na lista de marcas do seu recurso.

![Nova marca salva no Portal do Azure](./media/virtual-machines-common-tag/azure-portal-saved-new-tag.png)

