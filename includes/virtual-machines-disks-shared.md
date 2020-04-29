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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81008494"
---
Os discos compartilhados do Azure (versão prévia) são um novo recurso para discos gerenciados do Azure que permitem anexar um disco gerenciado a várias VMs (máquinas virtuais) simultaneamente. A anexação de um disco gerenciado a várias VMs permite que você implante novos aplicativos em cluster existentes ou migre-os para o Azure.

## <a name="how-it-works"></a>Como isso funciona

As VMs no cluster podem ler ou gravar no disco anexado com base na reserva escolhida pelo aplicativo clusterizado usando [reservas persistentes de SCSI](https://www.t10.org/members/w_spc3.htm) (PR. SCSI). O SCSI PR é um padrão do setor utilizado por aplicativos executados na rede de área de armazenamento (SAN) local. Habilitar o SCSI PR em um disco gerenciado permite que você migre esses aplicativos para o Azure no estado em que se encontram.

O compartilhamento de discos gerenciados oferece armazenamento de bloco compartilhado que pode ser acessado de várias VMs, eles são expostos como LUNs (números de unidade lógica). Em seguida, os LUNs são apresentados a um iniciador (VM) de um destino (disco). Esses LUNs são semelhantes ao DAS (Direct-Attached-Storage) ou a uma unidade local para a VM.

Os discos gerenciados compartilhados não oferecem nativamente um sistema de arquivos totalmente gerenciado que pode ser acessado usando SMB/NFS. Você precisa usar um Gerenciador de cluster, como o WSFC (cluster de failover do Windows Server) ou pacemaker, que manipula a comunicação do nó de cluster, bem como o bloqueio de gravação.

## <a name="limitations"></a>Limitações

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

## <a name="disk-sizes"></a>Tamanhos do disco

[!INCLUDE [virtual-machines-disks-shared-sizes](virtual-machines-disks-shared-sizes.md)]

## <a name="sample-workloads"></a>Cargas de trabalho de exemplo

### <a name="windows"></a>Windows

A maioria dos clusters baseados em Windows é criada no WSFC, que lida com toda a infraestrutura básica para comunicação de nó de cluster, permitindo que seus aplicativos aproveitem os padrões de acesso paralelo. O WSFC permite opções de CSV e não baseadas em CSV, dependendo da sua versão do Windows Server. Para obter detalhes, consulte [criar um cluster de failover](https://docs.microsoft.com/windows-server/failover-clustering/create-failover-cluster).

Alguns aplicativos populares em execução no WSFC incluem:

- SQL Server FCI (instâncias de cluster de failover)
- Servidor de arquivos de escalabilidade horizontal (SoFS)
- Servidor de arquivos para uso geral (carga de trabalho IW)
- Disco de perfil de usuário do Área de Trabalho Remota Server (RDS UPD)
- SAP ASCS/SCS

### <a name="linux"></a>Linux

Os clusters do Linux podem aproveitar os gerenciadores de cluster, como o [pacemaker](https://wiki.clusterlabs.org/wiki/Pacemaker). O pacemaker se baseia no [corosync](http://corosync.github.io/corosync/), permitindo a comunicação de cluster para aplicativos implantados em ambientes altamente disponíveis. Alguns sistemas de filecluster comuns incluem [OCFS2](https://oss.oracle.com/projects/ocfs2/) e [gfs2](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/global_file_system_2/ch-overview-gfs2). Você pode manipular reservas e registros usando utilitários como [fence_scsi](http://manpages.ubuntu.com/manpages/eoan/man8/fence_scsi.8.html) e [sg_persist](https://linux.die.net/man/8/sg_persist).

## <a name="persistent-reservation-flow"></a>Fluxo de reserva persistente

O diagrama a seguir ilustra um exemplo de aplicativo de banco de dados clusterizado de 2 nós que aproveita o SCSI PR para habilitar o failover de um nó para o outro.

![Cluster de dois nós. Um aplicativo em execução no cluster está manipulando o acesso ao disco](media/virtual-machines-disks-shared-disks/shared-disk-updated-two-node-cluster-diagram.png)

O fluxo é da seguinte maneira:

1. O aplicativo clusterizado em execução no Azure VM1 e VM2 registra sua intenção de ler ou gravar no disco.
1. A instância do aplicativo em VM1, em seguida, usa reserva exclusiva para gravar no disco.
1. Essa reserva é imposta em seu disco do Azure e o banco de dados agora pode gravar exclusivamente no disco. Qualquer gravação da instância do aplicativo em VM2 não terá sucesso.
1. Se a instância do aplicativo em VM1 falhar, a instância no VM2 agora poderá iniciar um failover de banco de dados e pegar o disco.
1. Essa reserva agora é imposta no disco do Azure e o disco não aceitará mais gravações de VM1. Ele só aceitará gravações de VM2.
1. O aplicativo clusterizado pode concluir o failover do banco de dados e atender as solicitações de VM2.

O diagrama a seguir ilustra outra carga de trabalho em cluster comum que consiste em vários nós lendo dados do disco para executar processos paralelos, como treinamento de modelos de aprendizado de máquina.

![Cluster de VM de quatro nós, cada nó registra a intenção de escrever, o aplicativo usa reserva exclusiva para lidar corretamente com os resultados de gravação](media/virtual-machines-disks-shared-disks/shared-disk-updated-machine-learning-trainer-model.png)

O fluxo é da seguinte maneira:

1. O aplicativo clusterizado em execução em todas as VMs registra a intenção de ler ou gravar no disco.
1. A instância do aplicativo no VM1 usa uma reserva exclusiva para gravar no disco ao abrir leituras no disco de outras VMs.
1. Essa reserva é imposta em seu disco do Azure.
1. Todos os nós no cluster agora podem ler do disco. Somente um nó grava os resultados no disco, em nome de todos os nós no cluster.

### <a name="ultra-disks-reservation-flow"></a>Fluxo de reserva de ultra discos

Ultra disks oferece um limite adicional, para um total de dois limites. Devido a isso, o fluxo de reserva de ultra discos pode funcionar conforme descrito na seção anterior, ou pode limitar e distribuir o desempenho de forma mais granular.

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-reservation-table.png" alt-text=" ":::

## <a name="ultra-disk-performance-throttles"></a>Acelerações de desempenho de ultra disco

Ultra discos têm o recurso exclusivo de permitir que você defina seu desempenho expondo atributos modificáveis e permitindo modificá-los. Por padrão, há apenas dois atributos modificáveis, mas os ultra discos compartilhados têm dois atributos adicionais.


|Atributo  |Descrição  |
|---------|---------|
|DiskIOPSReadWrite     |O número total de IOPS permitido em todas as VMs montando o disco de compartilhamento com acesso de gravação.         |
|DiskMBpsReadWrite     |A taxa de transferência total (MB/s) permitida em todas as VMs montando o disco compartilhado com acesso de gravação.         |
|DiskIOPSReadOnly*     |O número total de IOPS permitidos em todas as VMs que montam o disco compartilhado como ReadOnly.         |
|DiskMBpsReadOnly*     |A taxa de transferência total (MB/s) permitida em todas as VMs montando o disco compartilhado como ReadOnly.         |

\*Aplica-se somente a ultra discos compartilhados

As fórmulas a seguir explicam como os atributos de desempenho podem ser definidos, pois eles são usuários modificáveis:

- DiskIOPSReadWrite/DiskIOPSReadOnly: 
    - Limites de IOPS de 300 IOPS/GiB, até um máximo de 160K IOPS por disco
    - Mínimo de 100 IOPS
    - DiskIOPSReadWrite + DiskIOPSReadOnly é pelo menos 2 IOPS/GiB
- DiskMBpsRead Write/DiskMBpsReadOnly:
    - O limite de taxa de transferência de um único disco é de 256 KiB/s para cada IOPS provisionado, até um máximo de 2000 MBps por disco
    - A taxa de transferência mínima garantida por disco é 4KiB/s para cada IOPS provisionado, com uma linha de base geral mínima de 1 MBps

### <a name="examples"></a>Exemplos

Os exemplos a seguir descrevem alguns cenários que mostram como a limitação pode funcionar com ultra discos compartilhados, especificamente.

#### <a name="two-nodes-cluster-using-cluster-shared-volumes"></a>Cluster de dois nós usando volumes compartilhados do cluster

Veja a seguir um exemplo de um WSFC de dois nós usando volumes compartilhados clusterizados. Com essa configuração, ambas as VMs têm acesso simultâneo de gravação ao disco, o que resulta na divisão do acelerador ReadWrite entre as duas VMs e o limite ReadOnly não ser usado.

:::image type="complex" source="media/virtual-machines-disks-shared-disks/ultra-two-node-example.png" alt-text="Exemplo de ultra de dois nós do CSV":::

:::image-end:::

#### <a name="two-node-cluster-without-cluster-share-volumes"></a>Cluster de dois nós sem volumes de compartilhamento de cluster

Veja a seguir um exemplo de um WSFC de 2 nós que não está usando volumes compartilhados clusterizados. Com essa configuração, apenas uma VM tem acesso de gravação ao disco. Isso faz com que o acelerador ReadWrite seja usado exclusivamente para a VM primária e a limitação somente leitura esteja sendo usada pelo secundário.

:::image type="complex" source="media/virtual-machines-disks-shared-disks/ultra-two-node-no-csv.png" alt-text="CSV dois nós nenhum exemplo de ultra Disk de CSV":::

:::image-end:::

#### <a name="four-node-linux-cluster"></a>Cluster Linux de quatro nós

Veja a seguir um exemplo de um cluster Linux de 4 nós com um único gravador e três leitores de expansão. Com essa configuração, apenas uma VM tem acesso de gravação ao disco. Isso faz com que o acelerador ReadWrite seja usado exclusivamente para a VM primária e o acelerador ReadOnly sendo dividido pelas VMs secundárias.

:::image type="complex" source="media/virtual-machines-disks-shared-disks/ultra-four-node-example.png" alt-text="Exemplo de ultra limitação de quatro nós":::

:::image-end:::