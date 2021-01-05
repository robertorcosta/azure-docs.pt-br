---
title: Conheça os termos da infraestrutura de BareMetal do Azure
description: Conheça os termos da infraestrutura de BareMetal do Azure.
ms.topic: conceptual
ms.date: 12/31/2020
ms.openlocfilehash: f11bc4cfdd463623010ed7b6677235344ec7cd62
ms.sourcegitcommit: 42922af070f7edf3639a79b1a60565d90bb801c0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/31/2020
ms.locfileid: "97829090"
---
# <a name="know-the-terms-for-baremetal-infrastructure"></a>Conheça os termos da infraestrutura BareMetal

Neste artigo, abordaremos alguns termos importantes do BareMetal.

- **Revisão**: há duas revisões de carimbo diferentes para carimbos de instância de BareMetal. Cada versão difere na arquitetura e na proximidade com os hosts da máquina virtual do Azure:
    - **Revisão 3** (Rev. 3): é o design original.
    - **Revisão 4** (Rev 4): é um novo design que fornece proximidade com os hosts da VM (máquina virtual) do Azure e reduz a latência entre as máquinas virtuais do Azure e as unidades da instância BareMetal. 
    - **Revisão 4,2** (Rev 4,2): é a última infraestrutura de BareMetal de marca que usa a arquitetura Rev 4 existente. Você pode acessar e gerenciar suas instâncias do BareMetal por meio do portal do Azure.  

- **Stamp**: define o tamanho da implantação interna da Microsoft de instâncias de BareMetal. Antes que as unidades de instância possam ser implantadas, um carimbo de instância BareMetal que consiste em racks de computação, rede e armazenamento deve ser implantado em um datacenter local. Essa implantação é chamada de carimbo de instância BareMetal ou da revisão 4,2.

- **Locatário**: um cliente implantado no carimbo de instância BareMetal é isolado em um *locatário.* Um locatário é isolado de outros locatários na camada de rede, de armazenamento e de computação. As unidades de armazenamento e de computação atribuídas aos diferentes locatários não podem ver um ao outro ou se comunicar umas com as outras no nível de carimbo de instância BareMetal. Um cliente pode escolher ter implantações em diferentes locatários. Mesmo assim, não há nenhuma comunicação entre os locatários no nível de selo da instância de BareMetal.

## <a name="next-steps"></a>Próximas etapas
Saiba como identificar e interagir com as unidades de instância do BareMetal por meio do [portal do Azure](workloads/sap/baremetal-infrastructure-portal.md).


 