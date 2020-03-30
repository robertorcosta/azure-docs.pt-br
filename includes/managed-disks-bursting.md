---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/29/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 871a3edf70690a09d3747703e8bc999dfcce967c
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385173"
---
O estouro de disco é suportado para SSDs premium. O estouro é suportado em qualquer tamanho de disco SSD premium <= 512 GiB (P20 ou abaixo). Esses tamanhos de disco suportam estourar com o melhor esforço e utilizam um sistema de crédito para gerenciar o estouro. Os créditos se acumulam em um balde estourado sempre que o tráfego de disco está abaixo da meta de desempenho provisionada para o tamanho do disco e consomem créditos quando o tráfego estoura além do alvo. O tráfego de disco é rastreado contra iOPS e largura de banda no alvo provisionado. A explosão de disco não contornará as limitações de tamanho da máquina virtual (VM) no IOPS ou no throughput.

O estouro de disco é ativado por padrão em novas implantações dos tamanhos de disco que o suportam. Os tamanhos de disco existentes, se eles suportam a explosão de disco, podem permitir a explosão através de qualquer um dos seguintes métodos:

- Desconecte e reconecte o disco.
- Pare e ligue a VM.

## <a name="burst-states"></a>Estados estourados

Todos os tamanhos de disco aplicáveis a estouro começarão com um balde de crédito de estouro completo quando o disco estiver conectado a uma máquina virtual. A duração máxima do estouro é determinada pelo tamanho do balde de crédito estourado. Você só pode acumular créditos não utilizados até o tamanho do balde de crédito. A qualquer momento, seu balde de crédito de estouro de disco pode estar em um dos três estados seguintes: 

- Acumulando, quando o tráfego de disco estiver usando menos do que a meta de desempenho provisionada. Você pode acumular crédito se o tráfego de disco estiver além de alvos IOPS ou largura de banda ou ambos. Você ainda pode acumular créditos de IO quando estiver consumindo a largura de banda total do disco, vice-versa.  

- Em declínio, quando o tráfego de disco está usando mais do que a meta de desempenho provisionada. O tráfego estourado consumirá créditos independentemente do IOPS ou da largura de banda. 

- Permanecendo constante, quando o tráfego de disco está exatamente na meta de desempenho provisionada. 

Os tamanhos de disco que fornecem suporte de estouro junto com as especificações de estouro são resumidos na tabela abaixo.

## <a name="regional-availability"></a>Disponibilidade regional

O estouro de disco está disponível em todas as regiões da Nuvem Pública.

## <a name="disk-sizes"></a>Tamanhos do disco

[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

## <a name="example-scenarios"></a>Cenários de exemplo

Para você ter uma ideia melhor de como isso funciona, aqui estão alguns exemplos de cenários:

- Um cenário comum que pode se beneficiar do estouro do disco é o inicialização mais rápido da VM e o lançamento de aplicativos em discos do SO. Tome um VM Linux com uma imagem de 8 GiB OS como exemplo. Se usarmos um disco P2 como disco do SO, o alvo provisionado é 120 IOPS e 25 MBps. Quando a VM começar, haverá um pico de leitura no disco do SISTEMA OPERACIONAL carregando os arquivos de inicialização. Com a introdução do estouro, você pode ler a velocidade máxima de explosão de 3500 IOPS e 170 MBps, acelerando o tempo de carga em pelo menos 6x. Após a inicialização da VM, o nível de tráfego no disco do SISTEMA OPERACIONAL geralmente é baixo, já que a maioria das operações de dados pelo aplicativo será contra os discos de dados conectados. Se o tráfego estiver abaixo da meta provisionada, você acumulará créditos.

- Se você estiver hospedando um ambiente de área de trabalho virtual remota, sempre que um usuário ativo lança um aplicativo como o AutoCAD, o tráfego de leitura para o disco do SO aumenta significativamente. Neste caso, o tráfego estourado consumirá créditos acumulados, permitindo que você ultrapasse o alvo provisionado e lance o aplicativo muito mais rápido.

- Um disco P1 tem um alvo provisionado de 120 IOPS e 25 MBps. Se o tráfego real no disco foi de 100 IOPS e 20 MBps no último intervalo de 1 segundo, então os 20 IOs e 5 MB não utilizados são creditados ao balde de estouro do disco. Os créditos na caçamba estourada podem ser usados posteriormente quando o tráfego exceder a meta provisionada, até o limite máximo de estouro. O limite máximo de estouro define o teto do tráfego de disco, mesmo que você tenha créditos estourados para consumir. Neste caso, mesmo que você tenha 10.000 IOs no balde de crédito, um disco P1 não pode emitir mais do que o estouro máximo de 3.500 IO por segundo.  
