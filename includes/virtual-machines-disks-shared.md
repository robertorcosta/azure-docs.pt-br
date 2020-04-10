---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: c3e5beaef7fcc9d407103834e2040957ff32984c
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/10/2020
ms.locfileid: "81008494"
---
A visualização de discos compartilhados do Azure é um novo recurso para discos gerenciados do Azure que permite anexar um disco gerenciado a várias máquinas virtuais (VMs) simultaneamente. Anexar um disco gerenciado a várias VMs permite que você implante aplicativos novos ou migrados em cluster existentes para o Azure.

## <a name="how-it-works"></a>Como ele funciona

As VMs no cluster podem ler ou gravar no disco anexado com base na reserva escolhida pelo aplicativo clustered usando [SCSI Persistent Reservations](https://www.t10.org/members/w_spc3.htm) (SCSI PR). O SCSI PR é um padrão do setor alavancado por aplicativos em execução na SAN (Storage Area Network, rede de área de armazenamento) no local. Ativar o SCSI PR em um disco gerenciado permite que você migre esses aplicativos para o Azure como está.

O compartilhamento de discos gerenciados oferece armazenamento de bloco compartilhado que pode ser acessado a partir de várias VMs, estes são expostos como números de unidade lógica (LUNs). Os LUNs são então apresentados a um iniciador (VM) a partir de um alvo (disco). Esses LUNs parecem DAS (Direct-Attached-Storage, armazenamento com conexão direta) ou uma unidade local para a VM.

Os discos gerenciados compartilhados não oferecem nativamente um sistema de arquivos totalmente gerenciado que pode ser acessado usando SMB/NFS. Você precisa usar um gerenciador de cluster, como o Windows Server Failover Cluster (WSFC) ou o Pacemaker, que lida com a comunicação do nó de cluster, bem como o bloqueio de gravação.

## <a name="limitations"></a>Limitações

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

## <a name="disk-sizes"></a>Tamanhos do disco

[!INCLUDE [virtual-machines-disks-shared-sizes](virtual-machines-disks-shared-sizes.md)]

## <a name="sample-workloads"></a>Cargas de trabalho amostrais

### <a name="windows"></a>Windows

A maioria dos clusterings baseados no Windows se baseia no WSFC, que lida com toda a infra-estrutura principal para comunicação de nó de cluster, permitindo que seus aplicativos aproveitem padrões de acesso paralelos. O WSFC permite opções baseadas em CSV e não-CSV, dependendo da sua versão do Windows Server. Para obter detalhes, consulte [Criar um cluster failover](https://docs.microsoft.com/windows-server/failover-clustering/create-failover-cluster).

Alguns aplicativos populares em execução no WSFC incluem:

- FCI (SQL Server Failover Cluster Instances, instâncias de cluster de falha de servidor sql)
- SoFS (Scale-out File Server, servidor de arquivos de saída de escala)
- Servidor de arquivos para uso geral (carga de trabalho de IW)
- Disco de perfil do usuário do servidor de desktop remoto (RDS UPD)
- SAP ASCS/SCS

### <a name="linux"></a>Linux

Os clusters Linux podem aproveitar os gerentes de cluster, como [o Pacemaker](https://wiki.clusterlabs.org/wiki/Pacemaker). O Pacemaker se baseia no [Corosync,](http://corosync.github.io/corosync/)permitindo comunicações de cluster para aplicativos implantados em ambientes altamente disponíveis. Alguns sistemas de arquivos agrupados comuns incluem [ocfs2](https://oss.oracle.com/projects/ocfs2/) e [gfs2](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/global_file_system_2/ch-overview-gfs2). Você pode manipular reservas e registros usando utilitários como [fence_scsi](http://manpages.ubuntu.com/manpages/eoan/man8/fence_scsi.8.html) e [sg_persist](https://linux.die.net/man/8/sg_persist).

## <a name="persistent-reservation-flow"></a>Fluxo de reserva persistente

O diagrama a seguir ilustra um aplicativo de banco de dados agrupado de 2 nós de amostra que aproveita o SCSI PR para habilitar failover de um nó para o outro.

![Dois aglomerados de nó. Um aplicativo em execução no cluster está manipulando o acesso ao disco](media/virtual-machines-disks-shared-disks/shared-disk-updated-two-node-cluster-diagram.png)

O fluxo é da seguinte maneira:

1. O aplicativo em cluster em execução tanto no Azure VM1 quanto no VM2 registra sua intenção de ler ou gravar no disco.
1. A instância do aplicativo no VM1, em seguida, requer reserva exclusiva para gravar no disco.
1. Essa reserva é aplicada no disco Azure e o banco de dados agora pode gravar exclusivamente para o disco. Quaisquer gravações da instância de aplicação no VM2 não terão sucesso.
1. Se a instância do aplicativo no VM1 for abaixo, a instância no VM2 agora pode iniciar um failover e a tomada de controle do disco.
1. Esta reserva agora é aplicada no disco Azure e o disco não aceitará mais gravações do VM1. Ele só aceitará gravações do VM2.
1. O aplicativo em cluster pode completar o failover do banco de dados e atender solicitações do VM2.

O diagrama a seguir ilustra outra carga de trabalho comum em cluster consistindo em vários nós lendo dados do disco para execução de processos paralelos, como o treinamento de modelos de aprendizagem de máquina.

![Quatro cluster VM de nó, cada nó registra intenção de gravação, aplicativo requer reserva exclusiva para lidar adequadamente com os resultados de gravação](media/virtual-machines-disks-shared-disks/shared-disk-updated-machine-learning-trainer-model.png)

O fluxo é da seguinte maneira:

1. O aplicativo em cluster em execução em todas as VMs registra a intenção de ler ou gravar no disco.
1. A instância do aplicativo no VM1 requer uma reserva exclusiva para gravar no disco enquanto abre leituras para o disco de outras VMs.
1. Esta reserva é aplicada no seu disco Azure.
1. Todos os nós do cluster agora podem ser lidos a partir do disco. Apenas um nó registra resultados de volta ao disco, em nome de todos os nós do cluster.

### <a name="ultra-disks-reservation-flow"></a>Fluxo de reserva de discos ultra

Os discos ultra oferecem um acelerador adicional, para um total de dois aceleradores. Devido a isso, o fluxo de reserva de discos ultra pode funcionar como descrito na seção anterior, ou pode acelerar e distribuir o desempenho de forma mais granular.

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-reservation-table.png" alt-text=" ":::

## <a name="ultra-disk-performance-throttles"></a>Aceleradores de desempenho de disco ultra

Os discos ultra têm a capacidade única de permitir que você defina seu desempenho expondo atributos modificáveis e permitindo que você modifique-os. Por padrão, há apenas dois atributos modificáveis, mas, os discos ultra compartilhados têm dois atributos adicionais.


|Atributo  |Descrição  |
|---------|---------|
|DiskIOPSReadWrite     |O número total de IOPS permitido em todas as VMs que montam o disco de compartilhamento com acesso à gravação.         |
|DiskMBpsReadWrite     |O throughput total (MB/s) é permitido em todas as VMs que montam o disco compartilhado com acesso à gravação.         |
|DiskIOPSReadOnly*     |O número total de IOPS permitido em todas as VMs que montam o disco compartilhado como ReadOnly.         |
|DiskMBpsReadOnly*     |O throughput total (MB/s) permitido em todas as VMs que montam o disco compartilhado como ReadOnly.         |

\*Aplica-se apenas a discos ultra compartilhados

As fórmulas a seguir explicam como os atributos de desempenho podem ser definidos, uma vez que são modificáveis pelo usuário:

- DiskIOPSReadWrite/DiskIOPSReadOnly: 
    - Limites de IOPS de 300 IOPS/GiB, até um máximo de 160K IOPS por disco
    - Mínimo de 100 IOPS
    - DiskIOPSReadWrite + DiskIOPSReadOnly é pelo menos 2 IOPS/GiB
- DiskMBpsRead Write/DiskMBpsReadOnly:
    - O limite de throughput de um único disco é de 256 KiB/s para cada IOPS provisionado, até um máximo de 2000 MBps por disco
    - O rendimento mínimo garantido por disco é de 4KiB/s para cada IOPS provisionado, com um mínimo de base total de 1 MBps

### <a name="examples"></a>Exemplos

Os exemplos a seguir mostram alguns cenários que mostram como o estrangulamento pode funcionar com discos ultra compartilhados, especificamente.

#### <a name="two-nodes-cluster-using-cluster-shared-volumes"></a>Cluster de dois nós usando volumes compartilhados de cluster

O seguinte é um exemplo de um WSFC de 2 nós usando volumes compartilhados agrupados. Com esta configuração, ambas as VMs têm acesso a gravação simultânea ao disco, o que resulta na divisão do acelerador ReadWrite entre as duas VMs e o acelerador ReadOnly não sendo usado.

:::image type="complex" source="media/virtual-machines-disks-shared-disks/ultra-two-node-example.png" alt-text="CSV dois node ultra exemplo":::

:::image-end:::

#### <a name="two-node-cluster-without-cluster-share-volumes"></a>Dois clusters de nó sem volumes de compartilhamento de cluster

A seguir, um exemplo de um WSFC de 2 nós que não está usando volumes compartilhados agrupados. Com essa configuração, apenas uma VM tem acesso à gravação do disco. Isso resulta no acelerador ReadWrite sendo usado exclusivamente para a VM primária e o acelerador ReadOnly sendo usado apenas pelo secundário.

:::image type="complex" source="media/virtual-machines-disks-shared-disks/ultra-two-node-no-csv.png" alt-text="CSV dois nós sem exemplo de disco ultra csv":::

:::image-end:::

#### <a name="four-node-linux-cluster"></a>Cluster Linux de quatro nós

A seguir, um exemplo de um cluster Linux de 4 nós com um único escritor e três leitores de escala. Com essa configuração, apenas uma VM tem acesso à gravação do disco. Isso resulta no acelerador ReadWrite sendo usado exclusivamente para a VM primária e o acelerador ReadOnly sendo dividido pelas VMs secundárias.

:::image type="complex" source="media/virtual-machines-disks-shared-disks/ultra-four-node-example.png" alt-text="Exemplo de ultra estrangulamento de quatro nós":::

:::image-end:::