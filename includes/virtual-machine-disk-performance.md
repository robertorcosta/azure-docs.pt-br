---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 07/07/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 66380e0bab0a83d086ebebb5e595908cecd11643
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/10/2020
ms.locfileid: "89663604"
---
Este artigo ajuda a esclarecer o desempenho do disco e como ele funciona quando você combina as máquinas virtuais do Azure e os discos do Azure. Ele também descreve como você pode diagnosticar afunilamentos para a e/s de disco e as alterações que você pode fazer para otimizar o desempenho.

## <a name="how-does-disk-performance-work"></a>Como funciona o desempenho do disco?
As máquinas virtuais do Azure têm limites de desempenho de taxa de transferência e IOPS com base no tipo e tamanho da máquina virtual. Discos do sistema operacional e discos de dados, que podem ser anexados a máquinas virtuais, têm seus próprios limites de IOPs e taxa de transferência. Quando seu aplicativo em execução em suas máquinas virtuais solicita mais IOPS ou taxa de transferência do que o que é alocado para a máquina virtual ou os discos anexados, o desempenho do aplicativo é limitado. Quando isso acontecer, o aplicativo terá um desempenho inferior e poderá levar a algumas consequências negativas, como maior latência. Vamos executar alguns exemplos para solidificar isso. Para facilitar o acompanhamento desses exemplos, veremos apenas o IOPs, mas a mesma lógica também se aplica à taxa de transferência.

## <a name="disk-io-capping"></a>Limitação de e/s de disco
Configurar:
- Standard_D8s_v3 
    - IOPS não armazenado em cache: 12.800
- Disco do sistema operacional E30
    - IOPS: 500 
- 2 discos de dados E30
    - IOPS: 500

![Limitação de nível de disco](media/vm-disk-performance/disk-level-throttling.jpg)

O aplicativo em execução na máquina virtual faz uma solicitação que exige 10.000 IOPs para a máquina virtual. Todos os quais são permitidos pela VM porque a máquina virtual Standard_D8s_v3 pode executar até 12.800 IOPs. Essas solicitações de IOPs de 10.000 são divididas em três solicitações diferentes para os diferentes discos. 1.000 IOPs são solicitados ao disco do sistema operacional e 4.500 IOPs são solicitados para cada disco de dados. Como todos os discos anexados são discos E30 e só podem tratar 500 IOPs, eles respondem com 500 IOPs cada. O desempenho do aplicativo é então limitado pelos discos anexados e só pode processar 1.500 IOPs. Ele pode estar trabalhando em desempenho máximo em 10.000 IOPS se forem usados discos de melhor desempenho, como SSD Premium discos p30.

## <a name="virtual-machine-io-capping"></a>E/s de máquina virtual com limitação
Configurar:
- Standard_D8s_v3 
    - IOPS não armazenado em cache: 12.800
- Disco do sistema operacional p30
    - IOPS: 5.000 
- 2 discos de dados p30 
    - IOPS: 5.000

![Limitação de nível de máquina virtual](media/vm-disk-performance/vm-level-throttling.jpg)

O aplicativo em execução na máquina virtual faz uma solicitação que exige 15.000 IOPs. Infelizmente, a máquina virtual Standard_D8s_v3 é provisionada apenas para lidar com 12.800 IOPs. A partir disso, o aplicativo é limitado pelos limites da máquina virtual e, em seguida, deve alocar o IOPs de 12.800 alocado. Esses 12.800 IOPs solicitados são divididos em três solicitações diferentes para os diferentes discos. 4.267 IOPs são solicitados ao disco do sistema operacional e 4.266 IOPs são solicitados para cada disco de dados. Como todos os discos anexados são discos p30, que podem lidar com 5.000 IOPs, eles respondem de volta com seus valores solicitados.