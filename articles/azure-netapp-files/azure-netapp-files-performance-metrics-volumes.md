---
title: Testes de benchmark de desempenho recomendados - Arquivos Azure NetApp
description: Saiba mais sobre as recomendações de teste de benchmark para desempenho de volume e métricas usando arquivos do Azure NetApp.
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
ms.date: 08/07/2019
ms.openlocfilehash: 8f354152c23dd7ad0413f27585d724f8070ca003
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551515"
---
# <a name="performance-benchmark-test-recommendations-for-azure-netapp-files"></a>Recomendações do teste de parâmetros de comparação de desempenho para o Azure NetApp Files

Este artigo fornece recomendações de teste de benchmark para desempenho de volume e métricas usando arquivos do Azure NetApp.

## <a name="overview"></a>Visão geral

Para entender as características de desempenho de um volume de Arquivos Do Azure NetApp, você pode usar a ferramenta de código aberto [FIO](https://github.com/axboe/fio) para executar uma série de benchmarks para simular uma variedade de cargas de trabalho. Fio pode ser instalado em sistemas operacionais baseados em Linux e Windows.  É uma excelente ferramenta para obter um instantâneo rápido de IOPS e throughput para um volume.

### <a name="vm-instance-sizing"></a>Dimensionamento da instância vm

Para obter melhores resultados, certifique-se de que você está usando uma instância de máquina virtual (VM) que é adequadamente dimensionada para realizar os testes. Os exemplos a seguir usam uma Standard_D32s_v3 instância. Para obter mais informações sobre os tamanhos das instâncias de VM, consulte [Tamanhos para máquinas virtuais Windows no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) para VMs baseados no Windows e [tamanhos para máquinas virtuais Linux no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para VMs baseadas em Linux.

### <a name="azure-netapp-files-volume-sizing"></a>Dimensionamento do volume de arquivos do Azure NetApp

Certifique-se de escolher o nível de serviço correto e o tamanho da cota de volume para o nível de desempenho esperado. Consulte [os níveis de serviço para arquivos do Azure NetApp](azure-netapp-files-service-levels.md) para obter mais informações.

### <a name="virtual-network-vnet-recommendations"></a>Recomendações de rede virtual (VNet)

Você deve realizar os testes de benchmark no mesmo VNet que o Azure NetApp Files. O exemplo abaixo demonstra a recomendação:

![Recomendações da VNet](../media/azure-netapp-files/azure-netapp-files-benchmark-testing-vnet.png)

## <a name="installation-of-fio"></a>Instalação do FIO

FIO está disponível em formato binário para Linux e Windows. Siga a seção Pacotes Binários no [FIO](https://github.com/axboe/fio) para instalar para a plataforma de sua escolha.

## <a name="fio-examples-for-iops"></a>Exemplos de FIO para IOPS 

Os exemplos de FIO nesta seção usam a seguinte configuração:
* Tamanho da ocorrência de VM: D32s_v3
* Nível e tamanho do serviço do pool de capacidade: Premium / 50 TiB
* Tamanho da cota de volume: 48 TiB

Os exemplos a seguir mostram as leituras e gravações aleatórias do FIO.

### <a name="fio-8k-block-size-100-random-reads"></a>FIO: Tamanho do bloco 8k 100% leituras aleatórias

`fio --name=8krandomreads --rw=randread --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128 --size=4G --runtime=600 --group_reporting`

### <a name="output-68k-read-iops-displayed"></a>Saída: 68k ler IOPS exibido

`Starting 4 processes`  
`Jobs: 4 (f=4): [r(4)][84.4%][r=537MiB/s,w=0KiB/s][r=68.8k,w=0 IOPS][eta 00m:05s]`

### <a name="fio-8k-block-size-100-random-writes"></a>FIO: Tamanho do bloco de 8k 100% aleatório escreve

`fio --name=8krandomwrites --rw=randwrite --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-73k-write-iops-displayed"></a>Saída: 73k gravação IOPS exibida

`Starting 4 processes`  
`Jobs: 4 (f=4): [w(4)][26.7%][r=0KiB/s,w=571MiB/s][r=0,w=73.0k IOPS][eta 00m:22s]`

## <a name="fio-examples-for-bandwidth"></a>Exemplos de FIO para largura de banda

Os exemplos nesta seção mostram que o FIO lê e escreve sequencialmente.

### <a name="fio-64k-block-size-100-sequential-reads"></a>FIO: 64k tamanho do bloco 100% seqüencial leituras

`fio --name=64kseqreads --rw=read --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-118-gbits-throughput-displayed"></a>Saída: 11,8 Gbit/s throughput exibido

`Starting 4 processes`  
`Jobs: 4 (f=4): [R(4)][40.0%][r=1313MiB/s,w=0KiB/s][r=21.0k,w=0 IOPS][eta 00m:09s]`

### <a name="fio-64k-block-size-100-sequential-writes"></a>FIO: 64k tamanho do bloco 100% seqüencial escreve

`fio --name=64kseqwrites --rw=write --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-122-gbits-throughput-displayed"></a>Saída: 12,2 Gbit/s throughput exibido

`Starting 4 processes`  
`Jobs: 4 (f=4): [W(4)][85.7%][r=0KiB/s,w=1356MiB/s][r=0,w=21.7k IOPS][eta 00m:02s]`

## <a name="volume-metrics"></a>Métricas de volume

Os dados de desempenho do Azure NetApp Files estão disponíveis através dos contadores do Azure Monitor. Os contadores estão disponíveis através do portal Azure e solicitações da API REST GET. 

Você pode visualizar dados históricos para obter as seguintes informações:
* Latência média de leitura 
* Latência média de gravação 
* Ler IOPS (média)
* Escrever IOPS (média)
* Tamanho lógico do volume (média)
* Tamanho do instantâneo de volume (média)

### <a name="using-azure-monitor"></a>Como usar o Azure Monitor 

Você pode acessar os contadores de arquivos do Azure NetApp por volume a partir da página Métricas, conforme mostrado abaixo:

![Métricas do Monitor do Azure](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-metrics.png)

Você também pode criar um painel no Azure Monitor for Azure NetApp Files indo para a página Métricas, filtrando para o NetApp e especificando os contadores de volume de interesse: 

![Painel do Azure Monitor](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-dashboard.png)

### <a name="azure-monitor-api-access"></a>Acesso à API do Monitor do Azure

Você pode acessar os contadores de arquivos do Azure NetApp usando chamadas de API REST. Consulte [métricas suportadas com o Azure Monitor: Microsoft.NetApp/netAppContas/capacityPools/Volumes](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftnetappnetappaccountscapacitypoolsvolumes) para contadores para pools de capacidade e volumes.

O exemplo a seguir mostra uma URL GET para visualização do tamanho do volume lógico:

`#get ANF volume usage`  
`curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/ANFACCOUNTGOESHERE/capacityPools/ANFPOOLGOESHERE/Volumes/ANFVOLUMEGOESHERE/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=VolumeLogicalSize`


## <a name="next-steps"></a>Próximas etapas

- [Níveis de serviço do Azure NetApp Files](azure-netapp-files-service-levels.md)
- [Parâmetros de comparação de desempenho para o Azure NetApp Files](azure-netapp-files-performance-benchmarks.md)