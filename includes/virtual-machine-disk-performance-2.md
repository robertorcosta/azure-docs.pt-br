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
ms.openlocfilehash: 65f6c239f34775efff6a2ea2e399064a7702606a
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/10/2020
ms.locfileid: "89663605"
---
![Documentação do Dsv3](media/vm-disk-performance/dsv3-documentation.jpg)

A taxa de transferência máxima do disco não **armazenado em cache** é o limite máximo de armazenamento padrão que a máquina virtual pode manipular. O limite máximo de taxa de transferência de armazenamento **em cache** é um limite separado quando você habilita o cache de host. Habilitar o cache de host pode ser feito ao criar sua máquina virtual e anexar discos. Você também pode ajustar para ativar e desativar o cache de host de seus discos em uma VM existente:

![Cache de host](media/vm-disk-performance/host-caching.jpg)

O cache de host pode ser ajustado para corresponder aos seus requisitos de carga de trabalho para cada disco. Você pode definir o cache do host para ser somente leitura para cargas de trabalho que só lêem operações e leitura/gravação para cargas de trabalho que fazem um saldo de operações de leitura e gravação. Se sua carga de trabalho não seguir nenhum desses padrões, infelizmente você não poderá usar o cache de host. 

Vamos continuar com um exemplo com nossa máquina virtual de Standard_D8s_v3. Exceto desta vez, Habilitaremos o cache de host nos discos e, agora, o limite de IOPS da VM será de 16.000 IOPS. Anexado à VM há três discos p30 subjacentes que podem lidar com 5.000 IOPS.

Configurar:
- Standard_D8s_v3 
    - IOPS em cache: 16.000
    - IOPS não armazenado em cache: 12.800
- Disco do sistema operacional p30 
    - IOPS: 5.000
    - Cache de host habilitado 
- 2 discos de dados p30
    - IOPS: 5.000
    - Cache de host habilitado

![Exemplo de cache de host](media/vm-disk-performance/host-caching-example-without-remote.jpg)

Agora, o aplicativo que usa esse Standard_D8s_v3 máquina virtual com o Caching habilitado faz uma solicitação de 15.000 IOPS. Essas solicitações são divididas como 5.000 IOPS para cada disco subjacente anexado e nenhum capping de desempenho ocorre.

## <a name="combined-uncached-and-cached-limits"></a>Limites combinados não armazenados em cache e armazenados em cache

Os limites de cache de uma máquina virtual são separados dos limites não armazenados em cache. Isso significa que você pode habilitar o cache de host em discos anexados a uma VM, enquanto também não permite o cache de host em outros discos para permitir que suas máquinas virtuais obtenham uma e/s de armazenamento total do limite em cache mais o limite não armazenado em cache. Vamos executar um exemplo disso para ajudar a solidificar como esses limites funcionam juntos e continuaremos com a configuração de Standard_D8s_v3 máquina virtual e os discos Premium conectados.

Configurar:
- Standard_D8s_v3 
    - IOPS em cache: 16.000
    - IOPS não armazenado em cache: 12.800
- Disco do sistema operacional p30 
    - IOPS: 5.000
    - Cache de host habilitado 
- 2 discos de dados p30 X 2
    - IOPS: 5.000
    - Cache de host habilitado
- 2 discos de dados p30 X 2
    - IOPS: 5.000
    - Cache de host desabilitado

![Exemplo de cache de host com armazenamento remoto](media/vm-disk-performance/host-caching-example-with-remote.jpg)

Agora, o aplicativo em execução no Standard_D8s_v3 máquina virtual com faz uma solicitação de 25.000 IOPS. Essa solicitação é dividida como 5.000 IOPS para cada disco subjacente anexado, em que três desses discos estão usando o cache de host e 2 dos discos não são. Como os 3 usando o cache de host estão dentro dos limites em cache de 16.000, essas solicitações são concluídas com êxito e nenhum capping de desempenho de armazenamento ocorre. Além disso, como os 2 discos que não usam o cache de host estão dentro dos limites não armazenados em cache de 12.800, essas solicitações também são concluídas com êxito e nenhum capping ocorre.

## <a name="metrics-for-disk-performance"></a>Métricas para o desempenho do disco
Temos métricas no Azure que fornecem informações sobre como as máquinas virtuais e os discos estão sendo executados. Essas métricas podem ser exibidas visualmente por meio do portal do Azure ou podem ser recuperadas por meio de uma chamada à API. As métricas são calculadas em intervalos de um minuto. As métricas a seguir estão disponíveis para obter informações sobre a e/s de disco e a e/SS de taxa de transferência:
- **Profundidade da fila de disco do so** – o número de solicitações de e/s pendentes atuais que aguardam para serem lidas ou gravadas no disco do sistema operacional.
- **Bytes de leitura do disco do so/s** – o número de bytes lidos em um segundo do disco do sistema operacional.
- **Operações de leitura de disco do so/s** – o número de operações de entrada que são lidas em um segundo do disco do sistema operacional.
- **Bytes de gravação de disco do sistema operacional/s** – o número de bytes gravados em um segundo do disco do sistema operacional.
- **Operações de gravação de disco do sistema operacional/s** – o número de operações de saída que são gravadas em um segundo do disco do sistema operacional.
- **Profundidade da fila do disco de dados** – o número de solicitações de e/s pendentes atuais que aguardam para serem lidas ou gravadas nos discos de dados.
- **Bytes de leitura do disco de dados/s** – o número de bytes lidos em um segundo dos discos de dados.
- **Operações de leitura de disco de dados/s** – o número de operações de entrada que são lidas em um segundo de um ou mais discos de dados.
- **Bytes de gravação do disco de dados/s** – o número de bytes gravados em um segundo dos discos de dados.
- **Operações de gravação de disco de dados/s** – o número de operações de saída que são gravadas em um segundo de um ou mais discos de dados.
- **Bytes de leitura de disco/s** – o número total de bytes lidos em um segundo de todos os discos anexados a uma VM.
- **Operações de leitura de disco/s** – o número de operações de entrada que são lidas em um segundo de todos os discos anexados a uma VM.
- **Bytes de gravação de disco/s** – o número de bytes gravados em um segundo de todos os discos anexados a uma VM.
- **Operações de gravação de disco/s** – o número de operações de saída que são gravadas em um segundo de todos os discos anexados a uma VM.

## <a name="storage-io-utilization-metrics"></a>Métricas de utilização de e/s de armazenamento
Métricas que ajudam a diagnosticar a e/s de disco com limitação:
- **Porcentagem consumida de IOPS de disco de dados** -a porcentagem calculada pelo IOPS de disco de dados foi concluída sobre o IOPS de disco de dados provisionado. Se esse valor for de 100%, seu aplicativo em execução será a e/s limitada do limite de IOPS do disco de dados.
- **Porcentagem consumida da largura de banda do disco de dados** -a porcentagem calculada pela taxa de transferência do disco de dados foi concluída na taxa de transferência do disco de dados provisionado Se esse valor for de 100%, seu aplicativo em execução será a e/s limitada do limite de largura de banda do disco de dados.
- **Porcentagem consumida de IOPS de disco do so** -a porcentagem calculada pelo IOPS de disco do sistema operacional concluída em relação à IOPS de disco do sistema operacional provisionado. Se esse valor for de 100%, seu aplicativo em execução será a e/s limitada do limite de IOPS do seu disco do sistema operacional.
- **Porcentagem consumida da largura de banda do disco do so** -a porcentagem calculada pela taxa de transferência do disco do sistema operacional concluída na taxa de transferência do disco do so provisionado Se esse valor for de 100%, seu aplicativo em execução será a e/s limitada do limite de largura de banda do disco do sistema operacional.