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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422112"
---
Colocar VMs em uma única região reduz a distância física entre as instâncias. Colocá-los dentro de uma única zona de disponibilidade também os aproximará fisicamente. No entanto, à medida que a pegada do Azure cresce, uma única zona de disponibilidade pode abranger vários data centers físicos, o que pode resultar em uma latência de rede impactando seu aplicativo. 

Para obter VMs o mais próximo possível, alcançando a menor latência possível, você deve implantá-los dentro de um grupo de colocação de proximidade.

Um grupo de colocação de proximidade é um agrupamento lógico usado para garantir que os recursos de computação do Azure estejam fisicamente localizados perto um do outro. Grupos de colocação de proximidade são úteis para cargas de trabalho onde a baixa latência é um requisito.


- Baixa latência entre VMs autônomos.
- Baixa latência entre VMs em um único conjunto de disponibilidade ou um conjunto de escala de máquina virtual. 
- Baixa latência entre VMs independentes, VMs em vários conjuntos de disponibilidade ou conjuntos de várias escalas. Você pode ter vários recursos de computação em um único grupo de colocação para reunir um aplicativo de várias camadas. 
- Baixa latência entre vários níveis de aplicativos usando diferentes tipos de hardware. Por exemplo, executando o back-end usando série Sm em um conjunto de disponibilidade e a parte frontal em uma instância de série D, em um conjunto de escala, em um único grupo de colocação de proximidade.


![Gráfico para grupos de colocação de proximidade](./media/virtual-machines-common-ppg/ppg.png)

## <a name="using-proximity-placement-groups"></a>Usando grupos de colocação de proximidade 

Um grupo de colocação de proximidade é um novo tipo de recurso no Azure. Você precisa criar um antes de usá-lo com outros recursos. Uma vez criado, ele pode ser usado com máquinas virtuais, conjuntos de disponibilidade ou conjuntos de escala de máquinas virtuais. Você especifica um grupo de colocação de proximidade ao criar recursos de computação que fornecem o ID do grupo de colocação de proximidade. 

Você também pode mover um recurso existente para um grupo de colocação de proximidade. Ao mover um recurso para um grupo de colocação de proximidade, você deve parar (desalocar) o ativo primeiro, pois ele será reimplantado potencialmente em um data center diferente na região para satisfazer a restrição de colocation. 

No caso de conjuntos de disponibilidade e conjuntos de escala de máquinas virtuais, você deve definir o grupo de colocação de proximidade no nível de recursos em vez das máquinas virtuais individuais. 

Um grupo de colocação de proximidade é uma restrição de colocation em vez de um mecanismo de fixação. Ele é fixado em um data center específico com a implantação do primeiro recurso para usá-lo. Uma vez que todos os recursos que utilizam o grupo de colocação de proximidade foram interrompidos (desalocados) ou excluídos, ele não será mais fixado. Portanto, ao usar um grupo de colocação de proximidade com várias séries de VM, é importante especificar todos os tipos necessários antecipadamente em um modelo quando possível ou seguir uma seqüência de implantação que melhorará suas chances de uma implantação bem-sucedida. Se sua implantação falhar, reinicie a implantação com o tamanho da VM que falhou como o primeiro tamanho a ser implantado.

## <a name="what-to-expect-when-using-proximity-placement-groups"></a>O que esperar ao usar grupos de colocação de proximidade 
Grupos de colocação de proximidade oferecem co-localização no mesmo data center. No entanto, como os grupos de colocação de proximidade representam uma restrição adicional de implantação, podem ocorrer falhas de alocação. Existem poucos casos de uso em que você pode ver falhas de alocação ao usar grupos de colocação de proximidade:

- Quando você pede a primeira máquina virtual no grupo de colocação de proximidade, o data center é automaticamente selecionado. Em alguns casos, uma segunda solicitação para uma máquina virtual diferente SKU, pode falhar se não existir nesse data center. Neste caso, um erro **de Solicitação de alocação excessiva** é retornado. Para evitar isso, tente alterar a ordem em que você implanta suas SKUs ou tenha ambos os recursos implantados usando um único modelo ARM.
-   No caso de cargas de trabalho elásticas, onde você adiciona e remove instâncias de VM, ter uma restrição de grupo de colocação de proximidade em sua implantação pode resultar em uma falha no cumprimento da solicitação, resultando em erro **de falha de alocação.** 
- Parar (desalocar) e iniciar suas VMs conforme necessário é outra maneira de alcançar a elasticidade. Uma vez que a capacidade não é mantida uma vez que você parar (delomar) uma VM, reiná-la novamente pode resultar em um erro **de Falha de alocação.**


## <a name="best-practices"></a>Práticas recomendadas 
- Para a menor latência, use grupos de colocação de proximidade juntamente com rede acelerada. Para obter mais informações, consulte [Criar uma máquina virtual Linux com rede acelerada](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou criar uma máquina virtual do Windows com rede [acelerada](/azure/virtual-network/create-vm-accelerated-networking-powershell?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- Implante todos os tamanhos de VM em um único modelo. Para evitar o pouso em hardware que não suporta todas as SKUs VM e tamanhos que você precisa, inclua todos os níveis de aplicativo em um único modelo para que todos eles sejam implantados ao mesmo tempo.
- Se você estiver roteirando sua implantação usando PowerShell, CLI `OverconstrainedAllocationRequest`ou SDK, você pode obter um erro de alocação . Neste caso, você deve parar/desalocar todas as VMs existentes e alterar a seqüência no script de implantação para começar com o VM SKU/tamanhos que falharam. 
- Ao reutilizar um grupo de colocação existente do qual as VMs foram excluídas, aguarde que a exclusão seja totalmente concluída antes de adicionar VMs a ele.
- Se a latência for sua prioridade, coloque as VMs em um grupo de colocação de proximidade e toda a solução em uma zona de disponibilidade. Mas, se a resiliência for sua prioridade máxima, espalhe suas instâncias em várias zonas de disponibilidade (um único grupo de colocação de proximidade não pode abranger zonas).
