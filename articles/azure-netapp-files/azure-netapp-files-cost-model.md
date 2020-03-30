---
title: Modelo de custo para Arquivos Azure NetApp | Microsoft Docs
description: Descreve o modelo de custo para arquivos Do Azure NetApp para gerenciar despesas do serviço.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: b-juche
ms.openlocfilehash: aea783b818550b8219e1a0498256280f61f678e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "70995111"
---
# <a name="cost-model-for-azure-netapp-files"></a>Modelo de custo para o Azure NetApp Files 

Entender o modelo de custo para o Azure NetApp Files ajuda você a gerenciar suas despesas com o serviço.

## <a name="calculation-of-capacity-consumption"></a>Cálculo do consumo de capacidade

O Azure NetApp Files é cobrado na capacidade de armazenamento provisionado.  A capacidade provisionada é alocada pela criação de pools de capacidade.  Os pools de capacidade são cobrados com base em $/provisionado-GiB/mês em incrementos por hora. O tamanho mínimo para um único pool de capacidade é de 4 TiB, e as piscinas de capacidade podem ser posteriormente expandidas em incrementos de 1-TiB. Os volumes são criados dentro de pools de capacidade.  Cada volume é atribuído a uma cota que diminui a capacidade provisionada dos pools. A cota que pode ser atribuída aos volumes varia de um mínimo de 100 GiB a um máximo de 100 TiB.  

Para um volume ativo, o consumo de capacidade contra a cota baseia-se na capacidade lógica (efetiva).

Se o consumo real de capacidade de um volume exceder sua cota de armazenamento, o volume pode continuar a crescer. As gravações ainda serão permitidas desde que o tamanho real do volume seja menor que o limite do sistema (100 TiB).  

A capacidade total utilizada em um pool de capacidade em relação ao seu valor provisionado é a soma do maior da cota atribuída ou do consumo real de todos os volumes dentro do pool: 

   ![Cálculo total da capacidade utilizada](../media/azure-netapp-files/azure-netapp-files-total-used-capacity.png)

O diagrama abaixo ilustra esses conceitos.  
* Temos um pool de capacidade com 4 TiB de capacidade provisionada.  A piscina contém três volumes.  
    * O volume 1 é atribuído a uma cota de 2 TiB e tem 800 GiB de consumo.  
    * O volume 2 é atribuído a uma cota de 1 TiB e tem 100 GiB de consumo.  
    * O Volume 3 é atribuído a uma cota de 500 GiB, mas tem 800 GiB de consumo (overage).  
* O pool de capacidade é medido para 4 TiB de capacidade (o valor provisionado).  
    3.8 TiB de capacidade é consumido (2 TiB e 1 TiB de cota dos Volumes 1 e 2, e 800 GiB de consumo real para o Volume 3). E 200 GiB de capacidade estão remanescentes.

   ![Pool de capacidade com três volumes](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-three-vols.png)

## <a name="overage-in-capacity-consumption"></a>Excesso de consumo de capacidade  

Quando a capacidade total utilizada de um pool excede sua capacidade provisionada, as gravações de dados ainda são permitidas.  Após o período de carência (uma hora), se a capacidade utilizada do pool ainda exceder sua capacidade provisionada, então o tamanho da piscina será automaticamente aumentado em incrementos de 1 TiB até que a capacidade provisionada seja maior do que a capacidade total utilizada.  Por exemplo, na ilustração acima, se o Volume 3 continuar a crescer e o consumo real atingir 1,2 TiB, então após o período de carência, o pool será automaticamente redimensionado para 5 TiB.  O resultado é que a capacidade do pool provisionado (5 TiB) excede a capacidade utilizada (4,2 TiB).  

## <a name="manual-changes-of-the-pool-size"></a>Mudanças manuais do tamanho da piscina  

Você pode aumentar ou diminuir manualmente o tamanho da piscina. No entanto, as seguintes restrições se aplicam:
* Limites mínimos e máximos de serviço  
    Veja o artigo sobre [limites de recursos](azure-netapp-files-resource-limits.md).
* Um incremento 1-TiB após a compra mínima inicial de 4-TiB
* Um incremento mínimo de faturamento de uma hora
* O tamanho da piscina provisionada não pode ser reduzido para menos do que a capacidade total utilizada na piscina.

## <a name="behavior-of-maximum-size-pool-overage"></a>Comportamento do excesso de piscina de tamanho máximo   

O tamanho máximo de um pool de capacidade que você pode criar ou redimensionar é 500 TiB.  Quando a capacidade total utilizada em um pool de capacidade exceder 500 TiB, ocorrerão as seguintes situações:
* Ainda serão permitidas gravações de dados (se o volume estiver abaixo do máximo do sistema de 100 TiB).
* Após o período de carência de uma hora, o pool será automaticamente redimensionado em incrementos de 1-TiB, até que a capacidade provisionada do pool exceda a capacidade total utilizada.
* A capacidade adicional de pool provisionada e faturada superior a 500 TiB não pode ser usada para atribuir cota de volume. Ele também não pode ser usado para expandir os limites de QoS de desempenho.  
    Consulte [os níveis de serviço](azure-netapp-files-service-levels.md) sobre os limites de desempenho e o dimensionamento de QoS.

O diagrama abaixo ilustra esses conceitos:
* Temos um pool de capacidade com um nível de armazenamento Premium e uma capacidade de 500 TiB. A piscina contém nove volumes.
    * Os volumes de 1 a 8 são atribuídos um contingente de 60 TiB cada.  A capacidade total utilizada é de 480 TiB.  
        Cada volume tem um limite de QoS de 3,75 GiB/s de throughput (60 TiB * 64 MiB/s).  
    * O volume 9 é atribuído a uma cota de 20 TiB.  
        O volume 9 tem um limite de QoS de 1,25 GiB/s de throughput (20 TiB * 64 MiB/s).
* Volume 9 é um cenário de excesso. Tem 25 TiB de consumo real.  
    * Após o período de carência de uma hora, o pool de capacidade será redimensionado para 505 TiB.  
        Ou seja, capacidade total utilizada = 8 * cota 60-TiB para volumes 1 a 8, e 25 TiB de consumo real para o Volume 9.
    * A capacidade faturada é 505 TiB.
    * A cota de volume para o Volume 9 não pode ser aumentada (porque a cota total atribuída para o pool não pode exceder 500 TiB).
    * O throughput adicional de QoS pode não ser atribuído (porque o QoS total para o pool ainda é baseado em 500 TiB).

   ![Piscina de capacidade com nove volumes](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-nine-vols.png)

## <a name="capacity-consumption-of-snapshots"></a>Consumo de capacidade de instantâneos 

O consumo de capacidade de snapshots no Azure NetApp Files é cobrado contra a cota do volume pai.  Como resultado, compartilha a mesma taxa de faturamento do pool de capacidade a que o volume pertence.  No entanto, ao contrário do volume ativo, o consumo instantâneo é medido com base na capacidade incremental consumida.  Os instantâneos do Azure NetApp Files são diferenciais na natureza. Dependendo da taxa de alteração dos dados, os snapshots geralmente consomem muito menos capacidade do que a capacidade lógica do volume ativo. Por exemplo, suponha que você tenha um instantâneo de um volume de 500 GiB que contém apenas 10 GiB de dados diferenciais. A capacidade cobrada contra a cota de volume para esse instantâneo seria de 10 GiB, não 500 GiB. 

## <a name="next-steps"></a>Próximas etapas

* [Página de preços do Azure NetApp Files](https://azure.microsoft.com/pricing/details/storage/netapp/)
* [Níveis de serviço do Azure NetApp Files](azure-netapp-files-service-levels.md)
* [Limites de recursos do Azure NetApp Files](azure-netapp-files-resource-limits.md)
