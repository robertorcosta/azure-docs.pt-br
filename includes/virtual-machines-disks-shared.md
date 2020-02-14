---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/13/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 1bdb4e40d7e173dcb2368f2f0cf645581647f6ee
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77202197"
---
Os discos compartilhados do Azure (versão prévia) são um novo recurso para discos gerenciados do Azure que permite anexar um disco gerenciado do Azure a várias VMs (máquinas virtuais) simultaneamente. A anexação de um disco gerenciado a várias VMs permite que você implante novos aplicativos em cluster existentes ou migre-os para o Azure.

## <a name="how-it-works"></a>Como ele funciona

As VMs no cluster podem ler ou gravar no disco anexado com base na reserva escolhida pelo aplicativo clusterizado usando [reservas persistentes de SCSI](https://www.t10.org/members/w_spc3.htm) (PR. SCSI). O SCSI PR é um padrão do setor conhecido utilizado por aplicativos executados na rede de área de armazenamento (SAN) local. Habilitar o SCSI PR em um disco gerenciado permite que você migre esses aplicativos para o Azure no estado em que se encontram.

Os discos gerenciados com discos compartilhados habilitados oferecem armazenamento de bloco compartilhado que pode ser acessado por várias VMs, isso é exposto como LUNs (números de unidade lógica). Em seguida, os LUNs são apresentados a um iniciador (VM) de um destino (disco). Esses LUNs são semelhantes ao DAS (Direct-Attached-Storage) ou a uma unidade local para a VM.

Discos gerenciados com discos compartilhados habilitados não oferecem nativamente um sistema de arquivos totalmente gerenciado que pode ser acessado usando SMB/NFS. Você precisará usar um Gerenciador de cluster, como o WSFC (cluster de failover do Windows Server) ou pacemaker, que manipula a comunicação do nó de cluster, bem como o bloqueio de gravação.

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

![Shared-Disk-updated-Two-node-cluster-Diagram. png](media/virtual-machines-disks-shared-disks/shared-disk-updated-two-node-cluster-diagram.png)

O fluxo é da seguinte maneira:

1. O aplicativo clusterizado em execução no Azure VM1 e VM2 registra sua intenção de ler ou gravar no disco.
1. A instância do aplicativo em VM1, em seguida, usa reserva exclusiva para gravar no disco.
1. Essa reserva é imposta em seu disco do Azure e o banco de dados agora pode gravar exclusivamente no disco. Qualquer gravação da instância do aplicativo em VM2 não terá sucesso.
1. Se a instância do aplicativo em VM1 falhar, a instância no VM2 agora poderá iniciar um failover de banco de dados e pegar o disco.
1. Essa reserva agora é imposta no disco do Azure e o disco não aceitará mais gravações de VM1. Ele só aceitará gravações de VM2.
1. O aplicativo clusterizado pode concluir o failover do banco de dados e atender as solicitações de VM2.

O diagrama a seguir ilustra outra carga de trabalho em cluster comum que consiste em vários nós lendo dados do disco para executar processos paralelos, como treinamento de modelos de aprendizado de máquina.

![Shared-Disk-updated-Machine-Learning-trainer-Model. png](media/virtual-machines-disks-shared-disks/shared-disk-updated-machine-learning-trainer-model.png)

O fluxo é da seguinte maneira:

1. O aplicativo clusterizado em execução em todas as VMs registra a intenção de ler ou gravar no disco.
1. A instância do aplicativo no VM1 usa uma reserva exclusiva para gravar no disco ao abrir leituras no disco de outras VMs.
1. Essa reserva é imposta em seu disco do Azure.
1. Todos os nós no cluster agora podem ler do disco. Somente um nó grava os resultados no disco, em nome de todos os nós no cluster.