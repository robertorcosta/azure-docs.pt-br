---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/18/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a14ae76e15c1adb59917e61fbcbdaa34a7efa2d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77472002"
---
A visualização de discos compartilhados do Azure é um novo recurso para discos gerenciados do Azure que permite anexar um disco gerenciado do Azure a várias máquinas virtuais (VMs) simultaneamente. Anexar um disco gerenciado a várias VMs permite que você implante aplicativos novos ou migrados em cluster existentes para o Azure.

## <a name="how-it-works"></a>Como ele funciona

As VMs no cluster podem ler ou gravar no disco anexado com base na reserva escolhida pelo aplicativo clustered usando [SCSI Persistent Reservations](https://www.t10.org/members/w_spc3.htm) (SCSI PR). O SCSI PR é um padrão do setor bem conhecido alavancado por aplicativos em execução na SAN (Storage Area Network, rede de área de armazenamento) no local. Ativar o SCSI PR em um disco gerenciado permite que você migre esses aplicativos para o Azure como está.

Discos gerenciados com discos compartilhados habilitados oferecem armazenamento de bloco compartilhado que pode ser acessado por várias VMs, isso é exposto como números de unidade lógica (LUNs). Os LUNs são então apresentados a um iniciador (VM) a partir de um alvo (disco). Esses LUNs parecem DAS (Direct-Attached-Storage, armazenamento com conexão direta) ou uma unidade local para a VM.

Discos gerenciados com discos compartilhados habilitados não oferecem nativamente um sistema de arquivos totalmente gerenciado que pode ser acessado usando SMB/NFS. Você precisará usar um gerenciador de clusters, como o Windows Server Failover Cluster (WSFC) ou o Pacemaker, que lida com a comunicação do nó de cluster, bem como o bloqueio de gravação.

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