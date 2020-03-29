---
title: Série B explodida - Azure Virtual Machines
description: Descreve a série B de tamanhos expansíveis de VM do Azure.
services: virtual-machines
author: ayshakeen
manager: gwallace
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: ayshak
ms.openlocfilehash: 5121ef8eb3123d830233e91968c69b331d255bd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78161073"
---
# <a name="b-series-burstable-virtual-machine-sizes"></a>Tamanhos expansíveis da máquina virtual da série B

As VMs da série B são ideais para cargas de trabalho que não precisam do desempenho completo da CPU continuamente, como servidores web, prova de conceitos, pequenos bancos de dados e ambientes de construção de desenvolvimento. Normalmente, essas cargas de trabalho têm requisitos de desempenho expansíveis. A série B permite a compra de um tamanho de VM com a linha de base de desempenho, e a instância da VM criará créditos quando estiver usando menos que a linha de base. Quando a VM acumular crédito, ela poderá ultrapassar a linha de base usando até 100% da vCPU quando seu aplicativo exigir um melhor desempenho de CPU.

A série B vem nos seguintes tamanhos de VM:

Armazenamento Premium: com suporte

Cache de armazenamento premium: não suportado

Migração ao vivo: Suportado

Atualizações de preservação de memória: suportado

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Base de desempenho da CPU da VM | Máximo desempenho da CPU da VM | Créditos Iniciais | Créditos bancários/hora | Máximo de créditos armazenados | Discos de dados máximos | Máximo de armazenamento em cache e de armazenamento temporário: IOPS/MBps | Máximo throughput de disco sem cache: IOPS/MBps | Máximo de NICs |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| Standard_B1ls<sup>1</sup> | 1  | 0.5 | 4   | 5%   | 100%  | 30  | 3   | 72   | 2  | 200/10    | 160/10    | 2  |
| Standard_B1s              | 1  | 1   | 4   | 10%  | 100%  | 30  | 6   | 144  | 2  | 400/10    | 320/10    | 2  |
| Standard_B1ms             | 1  | 2   | 4   | 20%  | 100%  | 30  | 12  | 288  | 2  | 800/10    | 640/10    | 2  |
| Standard_B2s              | 2  | 4   | 8   | 40%  | 200%  | 60  | 24  | 576  | 4  | 1600/15   | 1280/15   | 3  |
| Standard_B2ms             | 2  | 8   | 16  | 60%  | 200%  | 60  | 36  | 864  | 4  | 2400/22.5 | 1920/22.5 | 3  |
| Standard_B4ms             | 4  | 16  | 32  | 90%  | 400%  | 120 | 54  | 1296 | 8  | 3600/35   | 2880/35   | 4  |
| Standard_B8ms             | 8  | 32  | 64  | 135% | 800%  | 240 | 81  | 1944 | 16 | 4320/50   | 4320/50   | 4  |
| Standard_B12ms            | 12 | 48  | 96  | 202% | 1200% | 360 | 121 | 2909 | 16 | 6480/75   | 4320/50   | 6  |
| Standard_B16ms            | 16 | 64  | 128 | 270% | 1600% | 480 | 162 | 3888 | 32 | 8640/100  | 4320/50   | 8  |
| Standard_B20ms            | 20 | 80  | 160 | 337% | 2000% | 600 | 203 | 4860 | 32 | 10800/125 | 4320/50   | 8  |

<sup>1</sup> B1ls é suportado apenas no Linux

## <a name="workload-example"></a>Exemplo de carga de trabalho

Considere um aplicativo de check-in/out do escritório. O aplicativo precisa de estouros de CPU durante o horário comercial, mas não muito poder de computação durante as horas de folga. Neste exemplo, a carga de trabalho requer uma máquina virtual de 16vCPU com 64GiB de RAM para funcionar de forma eficiente.

A tabela mostra os dados de tráfego de hora em hora e o gráfico é uma representação visual desse tráfego.

Características b16:

Perf de CPU máxima: 16vCPU * 100% = 1600%

Linha de base: 270%

![Gráfico de dados de tráfego por hora](./media/b-series-burstable/office-workload.png)

| Cenário | Hora | Uso de CPU (%) | Créditos acumulados<sup>1</sup> | Créditos disponíveis |
| --- | --- | --- | --- | --- |
| Implantação de B16ms | Implantação | Implantação  | 480 (Créditos Iniciais) | 480 |
| Sem tráfego | 0:00 | 0 | 162 | 642 |
| Sem tráfego | 1:00 | 0 | 162 | 804 |
| Sem tráfego | 2:00 | 0 | 162 | 966 |
| Sem tráfego | 3:00 | 0 | 162 | 1128 |
| Sem tráfego | 4:00 | 0 | 162 | 1290 |
| Sem tráfego | 5:00 | 0 | 162 | 1452 |
| Tráfego baixo | 6:00 | 270 | 0 | 1452 |
| Funcionários vêm ao escritório (app precisa de 80% de vCPU) | 7:00 | 1280 | -606 | 846 |
| Os funcionários continuam chegando ao escritório (o aplicativo precisa de 80% de vCPU) | 8:00 | 1280 | -606 | 240 |
| Tráfego baixo | 9:00 | 270 | 0 | 240 |
| Tráfego baixo | 10:00 | 100 | 102 | 342 |
| Tráfego baixo | 11:00 | 50 | 132 | 474 |
| Tráfego baixo | 12:00 | 100 | 102 | 576 |
| Tráfego baixo | 13:00 | 100 | 102 | 678 |
| Tráfego baixo | 14:00 | 50 | 132 | 810 |
| Tráfego baixo | 15:00 | 100 | 102 | 912 |
| Tráfego baixo | 16:00 | 100 | 102 | 1014 |
| Funcionários verificando (app precisa de 100% de vCPU) | 17:00 | 1600 | -798 | 216 |
| Tráfego baixo | 18:00 | 270 | 0 | 216 |
| Tráfego baixo | 19:00 | 270 | 0 | 216 |
| Tráfego baixo | 20:00 | 50 | 132 | 348 |
| Tráfego baixo | 21:00 | 50 | 132 | 480 |
| Sem tráfego | 22:00 | 0 | 162 | 642 |
| Sem tráfego | 23:00 | 0 | 162 | 804 |

<sup>1</sup> Os créditos acumulados/créditos `((Base CPU perf of VM - CPU Usage) / 100) * 60 minutes`utilizados em uma hora equivalem a: .  

Para um D16s_v3 que tem 16 vCPUs e 64 GiB de memória, a taxa horária é de US$ 0,936 por hora (mensal $ 673,92) e para B16ms com 16 vCPUs e 64 GiB de memória a taxa é de US $ 0,794 por hora (mensal $ 547,86). <b>Isso resulta em uma economia de 15%!</b>

## <a name="q--a"></a>Perguntas e respostas

### <a name="q-how-do-you-get-135-baseline-performance-from-a-vm"></a>P: como obter 135% da linha de base de desempenho de uma VM?

**R**: os 135% são compartilhados entre as 8 vCPUs que compõem o tamanho da VM. Por exemplo, se seu aplicativo utiliza 4 dos 8 núcleos trabalhando em processamento de lotes e cada uma das 4 vCPUs estão sendo executadas a 30% da utilização, o desempenho total da CPU da VM será de 120%.  Isso significa que a VM estaria criando créditos de tempo com base no delta de 15% da sua linha de base de desempenho.  Mas isso também significa que quando você tem créditos disponíveis, a mesma VM pode usar 100% de todas as 8 vCPUs, o que daria à VM um desempenho máximo de CPU de 800%.


### <a name="q-how-can-i-monitor-my-credit-balance-and-consumption"></a>P: como posso monitorar meu saldo e consumo?

**R**: apresentaremos duas novas métricas nas próximas semanas. A métrica **Credit** permitirá ver quantos créditos a sua VM acumulou e a métrica**ConsumedCredit** mostrará quantos créditos de CPU sua VM consumiu do banco.    Você poderá exibir essas métricas no painel de métricas no portal ou programaticamente pelas APIs do Azure Monitor.

Para saber mais sobre como acessar os dados de métrica do Azure, confira [Visão geral das métricas no Microsoft Azure](../azure-monitor/platform/data-platform.md).

### <a name="q-how-are-credits-accumulated"></a>P: como os créditos são acumulados?

**R**: as taxas de consumo e acumulação da VM estão definidas de modo que uma VM em execução na sua linha de base de desempenho não terá acúmulo ou consumo de créditos.  Uma VM terá um aumento de créditos sempre que estiver em execução abaixo da linha de base de desempenho e terá uma redução nos créditos sempre que a VM estiver usando a CPU acima da sua linha de base de desempenho.

**Exemplo**: implantei uma VM usando o tamanho B1ms para meu aplicativo de banco de dados pequeno. Esse tamanho permite que o meu aplicativo use até 20% de uma vCPU como minha linha de base, o que significa 0,2 de crédito por minuto que posso usar ou acumular.

Meu aplicativo está ocupado no início e no final do dia de trabalho dos meus funcionários, de 7h às 9h e de 16h às 18h. Durante as outras 20 horas do dia, meu aplicativo está normalmente ocioso, usando apenas 10% da vCPU. No horário fora de pico, acumulo 0,2 de crédito por minuto, mas consumo 0,1 de crédito por minuto, ou seja, minha VM acumulará 0,1 x 60 = 6 créditos por hora.  Nas 20 horas em que estou fora de pico, acumularei 120 créditos.  

Durante o horário de pico, meu aplicativo aproveita 60% de utilização de vCPU, ainda acumulo 0,2 de crédito por minuto, mas consumo 0,6 de crédito por minuto, com um custo líquido de 0,4 de crédito por minuto, ou 0,4 x 60 = 24 créditos por hora. Tenho 4 horas por dia de pico, ou seja, meu uso em hora de pico é de 4 x 24 = 96 créditos.

Se eu usar os 120 créditos acumulados fora de pico e subtrair os 96 créditos que usei para meu horário de pico, acumulo mais 24 créditos por dia para usar em outras atividades.

### <a name="q-how-can-i-calculate-credits-accumulated-and-used"></a>P: Como posso calcular créditos acumulados e usados?

**A:** Você pode usar a seguinte fórmula:

(Fundo de CPU perf de VM - Uso da CPU) / 100 = Banco de créditos ou uso por minuto

por exemplo, em instância acima, sua linha de base é de 20% e se você usar 10% da CPU que você está acumulando (20%-10%/100 = 0,1 crédito por minuto.

### <a name="q-does-the-b-series-support-premium-storage-data-disks"></a>P: a série B dá suporte a discos de dados de Armazenamento Premium?

**R**: sim, todos os tamanhos da série B dão suporte a discos de dados de Armazenamento Premium.

### <a name="q-why-is-my-remaining-credit-set-to-0-after-a-redeploy-or-a-stopstart"></a>P: Por que meu conjunto de créditos restantes está definido como 0 após uma reimplantação ou parar/iniciar?

**R** : Quando uma VM for “REIMPLANTADA”, ou seja, a VM se move para outro nó e o crédito acumulado é perdido. Se a VM for iniciada/interrompida, mas permanecer no mesmo nó, a VM retém o crédito acumulado. Sempre que a VM iniciar de novo em um nó, ele recebe uma crédito inicial, para Standard_B8ms de 240 minutos.

### <a name="q-what-happens-if-i-deploy-an-unsupported-os-image-on-b1ls"></a>P: O que acontece se eu implantar uma imagem do sistema operacional sem suporte em B1ls?

**A** : O B1ls só suporta imagens Linux e se você implantar qualquer outra imagem do Sistema Operacional, talvez não obtenha a melhor experiência do cliente.

## <a name="other-sizes"></a>Outros tamanhos

- [Propósito geral](sizes-general.md)
- [Otimizado para computação](sizes-compute.md)
- [Memória otimizada](sizes-memory.md)
- [Otimizado para armazenamento](sizes-storage.md)
- [GPU otimizada](sizes-gpu.md)
- [Computação de alto desempenho](sizes-hpc.md)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como as [ACUs (unidade de computação do Azure)](acu.md) podem ajudar você a comparar o desempenho de computação entre SKUs do Azure.