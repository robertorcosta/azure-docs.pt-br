---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/30/2019
ms.author: zivr
ms.custom: include file
ms.openlocfilehash: fb2eb2d237a1245627bbdb6f4f2eacbb9966a2c6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81422112"
---
Colocar VMs em uma única região reduz a distância física entre as instâncias. Colocá-los em uma única zona de disponibilidade também os colocará fisicamente juntos. No entanto, à medida que a superfície do Azure cresce, uma única zona de disponibilidade pode abranger vários data centers físicos, o que pode resultar em uma latência de rede impactando seu aplicativo. 

Para obter as VMs o mais próximo possível, alcançando a menor latência possível, você deve implantá-las em um grupo de posicionamento de proximidade.

Um grupo de posicionamento de proximidade é um agrupamento lógico usado para garantir que os recursos de computação do Azure estejam fisicamente localizados próximos um do outro. Os grupos de posicionamento de proximidade são úteis para cargas de trabalho em que a baixa latência é um requisito.


- Baixa latência entre VMs autônomas.
- Baixa latência entre VMs em um único conjunto de disponibilidade ou um conjunto de dimensionamento de máquinas virtuais. 
- Baixa latência entre VMs autônomas, VMs em vários conjuntos de disponibilidade ou vários conjuntos de dimensionamento. Você pode ter vários recursos de computação em um único grupo de posicionamento para reunir um aplicativo em várias camadas. 
- Baixa latência entre várias camadas de aplicativo usando tipos de hardware diferentes. Por exemplo, executar o back-end usando a série M em um conjunto de disponibilidade e o front-end em uma instância da série D, em um conjunto de dimensionamento, em um único grupo de posicionamento de proximidade.


![Gráfico para grupos de posicionamento de proximidade](./media/virtual-machines-common-ppg/ppg.png)

## <a name="using-proximity-placement-groups"></a>Usando grupos de posicionamento de proximidade 

Um grupo de posicionamento de proximidade é um novo tipo de recurso no Azure. Você precisa criar um antes de usá-lo com outros recursos. Depois de criado, ele pode ser usado com máquinas virtuais, conjuntos de disponibilidade ou conjuntos de dimensionamento de máquinas virtuais. Você especifica um grupo de posicionamento de proximidade ao criar recursos de computação, fornecendo a ID de grupo de posicionamento de proximidade. 

Você também pode mover um recurso existente para um grupo de posicionamento de proximidade. Ao mover um recurso para um grupo de posicionamento de proximidade, você deve parar (desalocar) o ativo primeiro, pois ele será reimplantado potencialmente em um data center diferente na região para atender à restrição de colocalização. 

No caso de conjuntos de disponibilidade e conjuntos de dimensionamento de máquinas virtuais, você deve definir o grupo de posicionamento de proximidade no nível de recurso em vez de máquinas virtuais individuais. 

Um grupo de posicionamento de proximidade é uma restrição de colocalização em vez de um mecanismo de fixação. Ele é fixado em um data center específico com a implantação do primeiro recurso para usá-lo. Depois que todos os recursos que usam o grupo de posicionamento de proximidade tiverem sido interrompidos (desalocados) ou excluídos, eles não serão mais fixados. Portanto, ao usar um grupo de posicionamento de proximidade com várias séries de VM, é importante especificar todos os tipos necessários antecipadamente em um modelo quando possível ou seguir uma sequência de implantação que melhorará suas chances de uma implantação bem-sucedida. Se sua implantação falhar, reinicie a implantação com o tamanho da VM que falhou como o primeiro tamanho a ser implantado.

## <a name="what-to-expect-when-using-proximity-placement-groups"></a>O que esperar ao usar grupos de posicionamento de proximidade 
Os grupos de posicionamento de proximidade oferecem colocalização na mesma data center. No entanto, como os grupos de posicionamento de proximidade representam uma restrição de implantação adicional, podem ocorrer falhas de alocação. Há alguns casos de uso em que você pode ver falhas de alocação ao usar grupos de posicionamento de proximidade:

- Quando você solicitar a primeira máquina virtual no grupo de posicionamento de proximidade, a data center será selecionada automaticamente. Em alguns casos, uma segunda solicitação para um SKU de máquina virtual diferente poderá falhar se não existir nesse data center. Nesse caso, um erro **OverconstrainedAllocationRequest** é retornado. Para evitar isso, tente alterar a ordem na qual você implanta suas SKUs ou ter os dois recursos implantados usando um único modelo ARM.
-   No caso de cargas de trabalho elásticas, em que você adiciona e remove instâncias de VM, ter uma restrição de grupo de posicionamento de proximidade em sua implantação pode resultar em uma falha ao atender à solicitação, resultando em erro **AllocationFailure** . 
- Parar (desalocar) e iniciar suas VMs conforme necessário é outra maneira de obter elasticidade. Como a capacidade não é mantida quando você parar (desalocar) uma VM, iniciá-la novamente pode resultar em um erro de **AllocationFailure** .


## <a name="best-practices"></a>Práticas recomendadas 
- Para a menor latência, use grupos de posicionamento de proximidade junto com a rede acelerada. Para obter mais informações, consulte [criar uma máquina virtual Linux com rede acelerada](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou [criar uma máquina virtual do Windows com rede acelerada](/azure/virtual-network/create-vm-accelerated-networking-powershell?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- Implante todos os tamanhos de VM em um único modelo. Para evitar a aterrissagem de hardware que não dá suporte a todos os tamanhos e SKUs de VM necessários, inclua todas as camadas de aplicativo em um único modelo para que todas sejam implantadas ao mesmo tempo.
- Se você estiver criando scripts para sua implantação usando o PowerShell, a CLI ou o SDK, poderá obter um `OverconstrainedAllocationRequest`erro de alocação. Nesse caso, você deve parar/desalocar todas as VMs existentes e alterar a sequência no script de implantação para começar com os tamanhos/SKU da VM que falharam. 
- Ao reutilizar um grupo de posicionamento existente do qual as VMs foram excluídas, aguarde até que a exclusão seja concluída completamente antes de adicionar VMs a ela.
- Se a latência for sua primeira prioridade, coloque as VMs em um grupo de posicionamento de proximidade e toda a solução em uma zona de disponibilidade. Mas, se a resiliência for sua prioridade máxima, espalhe suas instâncias entre várias zonas de disponibilidade (um único grupo de posicionamento de proximidade não pode abranger zonas).
