---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/28/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 6740ea320f2d950386da12eb44726e2c826b60a4
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2020
ms.locfileid: "80386116"
---
## <a name="premium-ssd"></a>SSD Premium

Os SSDs Premium do Azure oferecem compatibilidade de disco de alto desempenho e baixa latência para VMs (máquinas virtuais) com cargas de trabalho com uso intensivo de E/S (entrada/saída). Para aproveitar a velocidade e o desempenho dos discos de armazenamento Premium, você pode migrar os discos de VM existentes para os SSDs Premium. Os SSDs Premium são adequados para aplicativos de produção críticos. SSDs premium só podem ser usados com séries VM compatíveis com armazenamento premium.

Para saber mais sobre os tipos e tamanhos individuais de VM no Azure for Windows, incluindo quais tamanhos são compatíveis com armazenamento premium, consulte [tamanhos de VM do Windows](../articles/virtual-machines/windows/sizes.md). Para saber mais sobre tipos e tamanhos individuais de VM no Azure para Linux, incluindo quais tamanhos são compatíveis com armazenamento premium, consulte [tamanhos de VM Linux](../articles/virtual-machines/linux/sizes.md). A partir de qualquer um desses artigos, você precisa verificar cada artigo de tamanho de VM individual para determinar se ele é compatível com armazenamento premium.

### <a name="disk-size"></a>Tamanho do disco
[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

Quando você provisiona um disco de armazenamento premium, ao contrário do armazenamento padrão, a capacidade, IOPS e taxa de transferência de disco são garantidos. Por exemplo, se você criar um disco P50, o Azure provisionará uma capacidade de armazenamento de 4.095 GB, 7.500 IOPS e uma taxa de transferência de 250 MB/s para o disco. O aplicativo pode usar a capacidade e o desempenho no todo ou em parte. Os discos SSD premium são projetados para fornecer latências de um dígito baixa e iOPS de destino e throughput descritos na tabela anterior 99,9% do tempo.

## <a name="bursting"></a>Estourando

Tamanhos SSD premium menores que p30 agora oferecem estouro de disco e podem estourar seu IOPS por disco até 3.500 e sua largura de banda até 170 Mbps. O estouro é automatizado e opera com base em um sistema de crédito. Os créditos são automaticamente acumulados em um balde estourado quando o tráfego de disco está abaixo da meta de desempenho provisionada e os créditos são automaticamente consumidos quando o tráfego estoura além do alvo, até o limite máximo de estouro. O limite máximo de estouro define o teto do disco IOPS & Largura de Banda mesmo se você tiver créditos estourados para consumir. A explosão de disco proporciona melhor tolerância a mudanças imprevisíveis de padrões de IO. Você pode aproveitá-lo melhor para inicialização de disco do SISTEMA OPERACIONAL e aplicativos com tráfego espetado.    

O suporte a estouro de discos será ativado em novas implantações de tamanhos de disco aplicáveis por padrão, sem necessidade de ação do usuário. Para os discos existentes dos tamanhos aplicáveis, você pode habilitar o estouro com qualquer uma das duas opções: desprender e reconectar o disco ou parar e reiniciar a VM anexada. Todos os tamanhos de disco aplicáveis a estouro começarão com um balde de crédito de estouro completo quando o disco estiver conectado a uma Máquina Virtual que suporta uma duração máxima no limite máximo de explosão de 30 min. Para saber mais sobre como o trabalho de estouro nos discos Do Azure, consulte [o SSD Premium estourando](../articles/virtual-machines/linux/disk-bursting.md). 

### <a name="transactions"></a>Transactions

Para SSDs premium, cada operação de I/O menor ou igual a 256 KiB de throughput é considerada uma única operação de I/O. Operações de I/O maiores que 256 KiB de throughput são consideradas I/Os múltiplas do tamanho 256 KiB.

## <a name="standard-ssd"></a>SSD Standard

Os SSDs Standard do Azure são uma opção econômica de armazenamento otimizado para cargas de trabalho que necessitam de desempenho consistente em níveis de IOPS mais baixos. SSD padrão oferece uma experiência de bom nível de entrada para aqueles que desejam migrar para a nuvem, especialmente se passar por problemas com a variação das cargas de trabalho em execução nas soluções de HD no local. Em comparação com os HDDs padrão, os SSDs padrão oferecem melhor disponibilidade, consistência, confiabilidade e latência. Os SSDs Standard são adequado para servidores Web, servidores de aplicativos de IOPS baixo, aplicativos empresariais pouco usados e cargas de trabalho de Desenvolvimento/Teste. Como os HDDs padrão, os SSDs padrão estão disponíveis em todas as VMs do Azure.

### <a name="disk-size"></a>Tamanho do disco
[!INCLUDE [disk-storage-standard-ssd-sizes](disk-storage-standard-ssd-sizes.md)]

Os SSDs padrão são projetados para fornecer latências de um dígito e o IOPS e throughput até os limites descritos na tabela anterior 99% do tempo. IOPS real e throughput podem variar às vezes dependendo dos padrões de tráfego. SSDs Padrão fornecerão um desempenho mais consistente do que os discos HD com a menor latência.

### <a name="transactions"></a>Transactions

Para SSDs padrão, cada operação de I/O menor ou igual a 256 KiB de throughput é considerada uma única operação de I/O. Operações de I/O maiores que 256 KiB de throughput são consideradas I/Os múltiplas do tamanho 256 KiB. Essas transações têm um impacto de faturamento.

## <a name="standard-hdd"></a>HDD Standard

Os HDs Standard do Azure oferecem compatibilidade de disco confiável e de baixo custo para VMs que executam cargas de trabalho insensíveis a latência. Com o Armazenamento Standard, os dados são armazenados em HDs (unidades de disco rígido). Latência, IOPS e Throughput de discos HDD padrão podem variar mais amplamente em comparação com discos baseados em SSD. Os discos HDD padrão são projetados para fornecer latências de gravação abaixo de 10ms e ler latências abaixo de 20ms para a maioria das operações de IO, no entanto, o desempenho real pode variar dependendo do tamanho do IO e do padrão de carga de trabalho. Ao trabalhar com VMs, você pode usar discos HDD padrão para cenários de dev/teste e cargas de trabalho menos críticas. Os HDDs padrão estão disponíveis em todas as regiões do Azure e podem ser usados com todas as VMs do Azure.

### <a name="disk-size"></a>Tamanho do disco
[!INCLUDE [disk-storage-standard-hdd-sizes](disk-storage-standard-hdd-sizes.md)]

### <a name="transactions"></a>Transactions

Para HDDs padrão, cada operação de IO é considerada como uma única transação, independentemente do tamanho de I/O. Essas transações têm um impacto de faturamento.

## <a name="billing"></a>Cobrança

Ao usar discos gerenciados, as seguintes considerações de cobrança se aplicam:

- Tipo de disco
- Tamanho do disco gerenciado
- Instantâneos
- Transferências de dados de saída
- Número de transações

**Tamanho do disco gerenciado**: discos gerenciados são cobrados no tamanho provisionado. O Azure mapeia o tamanho provisionado (arredondado) para o tamanho de disco oferecido mais próximo. Para obter detalhes sobre os tamanhos de disco oferecidos, confira as tabelas anteriores. Cada disco é mapeado para um tamanho de disco provisionado compatível e é cobrado de acordo. Por exemplo, se você provisionar uma SSD Standard de 200 GiB, ela será mapeada para a oferta de tamanho de disco E15 (256 GiB). A cobrança por qualquer disco provisionado é rateada por hora usando o preço mensal para a oferta de Armazenamento Premium. Por exemplo, se você provisionou um disco E10 e o excluiu após 20 horas, será cobrado pela a oferta E10 rateada em 20 horas. Isso é independente da quantidade de dados reais gravados no disco.

**Instantâneos**: Os instantâneos são cobrados com base no tamanho utilizado. Por exemplo, se você criar um instantâneo de um disco gerenciado com capacidade provisionada de 64 GiB e tamanho real de dados usados de 10 GiB, o instantâneo será cobrado apenas pelo tamanho de dados usados de 10 GiB.
