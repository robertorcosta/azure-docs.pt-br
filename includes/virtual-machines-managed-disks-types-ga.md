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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80386116"
---
## <a name="premium-ssd"></a>SSD Premium

Os SSDs Premium do Azure oferecem compatibilidade de disco de alto desempenho e baixa latência para VMs (máquinas virtuais) com cargas de trabalho com uso intensivo de E/S (entrada/saída). Para aproveitar a velocidade e o desempenho dos discos de armazenamento Premium, você pode migrar os discos de VM existentes para os SSDs Premium. Os SSDs Premium são adequados para aplicativos de produção críticos. O SSDs Premium só pode ser usado com a série de VMs que são compatíveis com o armazenamento Premium.

Para saber mais sobre os tipos e tamanhos de VM individuais no Azure para Windows, incluindo quais tamanhos são compatíveis com o armazenamento Premium, consulte [tamanhos de VM do Windows](../articles/virtual-machines/windows/sizes.md). Para saber mais sobre os tipos e tamanhos de VM individuais no Azure para Linux, incluindo quais tamanhos são compatíveis com o armazenamento Premium, consulte [tamanhos de VM do Linux](../articles/virtual-machines/linux/sizes.md). A partir de qualquer um desses artigos, você precisa verificar cada artigo de tamanho de VM individual para determinar se ele é compatível com o armazenamento Premium.

### <a name="disk-size"></a>Tamanho do disco
[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

Quando você provisiona um disco de armazenamento premium, ao contrário do armazenamento padrão, a capacidade, IOPS e taxa de transferência de disco são garantidos. Por exemplo, se você criar um disco P50, o Azure provisionará uma capacidade de armazenamento de 4.095 GB, 7.500 IOPS e uma taxa de transferência de 250 MB/s para o disco. O aplicativo pode usar a capacidade e o desempenho no todo ou em parte. SSD Premium discos são projetados para fornecer latências de milissegundos de um dígito baixo e IOPS de destino e taxa de transferência descritas na tabela anterior 99,9% do tempo.

## <a name="bursting"></a>Bursting

SSD Premium tamanhos menores que p30 agora oferecem intermitência de disco e podem estourar seus IOPS por disco até 3.500 e sua largura de banda de até 170 Mbps. A intermitência é automatizada e opera com base em um sistema de crédito. Os créditos são acumulados automaticamente em um Bucket de intermitência quando o tráfego de disco está abaixo do destino de desempenho provisionado e os créditos são consumidos automaticamente quando o tráfego ultrapassa o destino, até o limite de intermitência máximo. O limite máximo de intermitência define o teto de IOPS de disco & largura de banda, mesmo se você tiver créditos de intermitência a serem consumidos. A intermitência de disco fornece melhor tolerância a alterações imprevisíveis de padrões de e/s. Você pode aproveitá-lo melhor para inicialização de disco do so e aplicativos com tráfego com picos.    

O suporte a intermitência de discos será habilitado em novas implantações de tamanhos de disco aplicáveis por padrão, sem a necessidade de ação do usuário. Para discos existentes dos tamanhos aplicáveis, você pode habilitar a intermitência com uma das duas opções: desanexar e anexar novamente o disco ou parar e reiniciar a VM conectada. Todos os tamanhos de disco aplicáveis de intermitência começarão com um Bucket de crédito de intermitência completa quando o disco for anexado a uma máquina virtual que dá suporte a uma duração máxima no limite de pico de intermitência de 30 minutos. Para saber mais sobre como a intermitência funciona nos discos do Azure, confira [SSD Premium intermitênciaing](../articles/virtual-machines/linux/disk-bursting.md). 

### <a name="transactions"></a>Transactions

Para o SSDs Premium, cada operação de e/s menor ou igual a 256 KiB de taxa de transferência é considerada uma única operação de e/s. Operações de e/s maiores que 256 KiB de taxa de transferência são consideradas várias e/SS de tamanho de 256 KiB.

## <a name="standard-ssd"></a>SSD Standard

Os SSDs Standard do Azure são uma opção econômica de armazenamento otimizado para cargas de trabalho que necessitam de desempenho consistente em níveis de IOPS mais baixos. SSD padrão oferece uma experiência de bom nível de entrada para aqueles que desejam migrar para a nuvem, especialmente se passar por problemas com a variação das cargas de trabalho em execução nas soluções de HD no local. Em comparação com HDDs padrão, o SSDs padrão oferece melhor disponibilidade, consistência, confiabilidade e latência. Os SSDs Standard são adequado para servidores Web, servidores de aplicativos de IOPS baixo, aplicativos empresariais pouco usados e cargas de trabalho de Desenvolvimento/Teste. Como HDDs padrão, o SSDs padrão está disponível em todas as VMs do Azure.

### <a name="disk-size"></a>Tamanho do disco
[!INCLUDE [disk-storage-standard-ssd-sizes](disk-storage-standard-ssd-sizes.md)]

O SSDs padrão é projetado para fornecer latências de milissegundos de dígito único e a IOPS e a taxa de transferência até os limites descritos na tabela anterior 99% do tempo. O IOPS e a taxa de transferência reais podem variar às vezes, dependendo dos padrões de tráfego. SSDs Padrão fornecerão um desempenho mais consistente do que os discos HD com a menor latência.

### <a name="transactions"></a>Transactions

Para SSDs padrão, cada operação de e/s menor ou igual a 256 KiB de taxa de transferência é considerada uma única operação de e/s. Operações de e/s maiores que 256 KiB de taxa de transferência são consideradas várias e/SS de tamanho de 256 KiB. Essas transações têm um impacto de cobrança.

## <a name="standard-hdd"></a>HDD Standard

Os HDs Standard do Azure oferecem compatibilidade de disco confiável e de baixo custo para VMs que executam cargas de trabalho insensíveis a latência. Com o Armazenamento Standard, os dados são armazenados em HDs (unidades de disco rígido). A latência, o IOPS e a taxa de transferência de discos de HDD Standard podem variar mais amplamente em comparação com discos baseados em SSD. HDD Standard discos são projetados para fornecer latências de gravação em 10 ms e latências de leitura em 20 ms para a maioria das operações de e/s, no entanto, o desempenho real pode variar dependendo do padrão de tamanho de e/s Ao trabalhar com VMs, você pode usar discos HDD padrão para cenários de desenvolvimento/teste e cargas de trabalho menos críticas. Os HDDs padrão estão disponíveis em todas as regiões do Azure e podem ser usados com todas as VMs do Azure.

### <a name="disk-size"></a>Tamanho do disco
[!INCLUDE [disk-storage-standard-hdd-sizes](disk-storage-standard-hdd-sizes.md)]

### <a name="transactions"></a>Transactions

Para HDDs padrão, cada operação de e/s é considerada como uma única transação, independentemente do tamanho de e/s. Essas transações têm um impacto de cobrança.

## <a name="billing"></a>Cobrança

Ao usar discos gerenciados, as seguintes considerações de cobrança se aplicam:

- Tipo de disco
- Tamanho do disco gerenciado
- Instantâneos
- Transferências de dados de saída
- Número de transações

**Tamanho do disco gerenciado**: discos gerenciados são cobrados no tamanho provisionado. O Azure mapeia o tamanho provisionado (arredondado) para o tamanho de disco oferecido mais próximo. Para obter detalhes sobre os tamanhos de disco oferecidos, confira as tabelas anteriores. Cada disco é mapeado para um tamanho de disco provisionado compatível e é cobrado de acordo. Por exemplo, se você provisionar uma SSD Standard de 200 GiB, ela será mapeada para a oferta de tamanho de disco E15 (256 GiB). A cobrança por qualquer disco provisionado é rateada por hora usando o preço mensal para a oferta de Armazenamento Premium. Por exemplo, se você provisionou um disco E10 e o excluiu após 20 horas, será cobrado pela a oferta E10 rateada em 20 horas. Isso é independente da quantidade de dados reais gravados no disco.

**Instantâneos**: os instantâneos são cobrados com base no tamanho usado. Por exemplo, se você criar um instantâneo de um disco gerenciado com capacidade provisionada de 64 GiB e tamanho real de dados usados de 10 GiB, o instantâneo será cobrado apenas pelo tamanho de dados usados de 10 GiB.
