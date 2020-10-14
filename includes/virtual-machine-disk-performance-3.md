---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 10/12/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 14e74bfbcd087ccc1d8c5f2f10a8e44ed37cce84
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92016419"
---
![Menu de métricas](media/vm-disk-performance/utilization-metrics-example/fio-output.jpg)

No entanto, o Standard_D8s_v3 pode alcançar um total de 28.600 IOPs, o uso das métricas permite investigar o que está acontecendo e identificar nosso afunilamento de e/s de armazenamento. Primeiro, localize o botão de métricas no menu do lado esquerdo e selecione-o:

![Menu de métricas](media/vm-disk-performance/utilization-metrics-example/metrics-menu.jpg)

Primeiro, vamos dar uma olhada em nossa métrica **percentual consumida de IOPS em cache de VM** :

![Porcentagem consumida de IOPS em cache da VM](media/vm-disk-performance/utilization-metrics-example/vm-cached.jpg)

Essa métrica está informando-nos os 16.000 IOPs alocados para o IOPs armazenado em cache na VM, 61% está sendo usado. Isso significa que o afunilamento de e/s de armazenamento não está com os discos armazenados em cache porque não está em 100%. Agora, vamos dar uma olhada em nossa métrica **percentual consumida de IOPS sem cache de VM** :

![Porcentagem consumida de IOPS não armazenado em cache da VM](media/vm-disk-performance/utilization-metrics-example/vm-uncached.jpg)

Essa métrica está em 100%, informando que todos os 12.800 IOPs alocados para o IOPs não armazenado em cache na VM estão sendo usados. Uma maneira de corrigir isso é alterando o tamanho de nossa VM para um tamanho maior que possa lidar com a e/s adicional. Mas, antes de fazermos isso, vamos dar uma olhada no disco anexado para ver quantos IOPs eles estão vendo. Primeiro, vamos dar uma olhada no disco do sistema operacional examinando a **porcentagem consumida de IOPS de disco do so**:

![Porcentagem consumida de IOPS de disco do so](media/vm-disk-performance/utilization-metrics-example/os-disk.jpg)

Essa métrica está nos informando que os 5.000 IOPs provisionados para esse disco do sistema operacional p30, em cerca de 90% de ti estão sendo usados. Isso significa que não há nenhum afunilamento aqui no disco do sistema operacional. Agora vamos dar uma olhada nos discos de dados que estão anexados à VM examinando a **porcentagem consumida IOPS de disco de dados**:

![Percentual de IOPS consumido de disco de dados](media/vm-disk-performance/utilization-metrics-example/data-disks-no-splitting.jpg)

Essa métrica está nos informando que a porcentagem média de IOPs consumidas em todos os discos anexados está em cerca de 42%. Esse percentual é calculado com base no IOPs usado pelos discos e não está sendo servido pelo cache do host. Vamos aprofundar essa métrica para ver o aplicando a **divisão** nessas métricas e dividindo pelo valor de LUN:

![Percentual de IOPS consumido de disco de dados com divisão](media/vm-disk-performance/utilization-metrics-example/data-disks-splitting.jpg)

Essa métrica está nos dizendo que os discos de dados anexados no LUN 3 e 2 estão sendo usado cerca de 85% de seus IOPs provisionados. Aqui está um diagrama do que a e/s está parecida com a arquitetura de VM e discos:

![Diagrama de exemplo de métricas de e/s de armazenamento](media/vm-disk-performance/utilization-metrics-example/metrics-diagram.jpg)
