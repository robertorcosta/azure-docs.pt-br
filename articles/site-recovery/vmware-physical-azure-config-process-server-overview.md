---
title: Sobre a configuração/process/master target do Azure Site
description: Este artigo fornece uma visão geral da configuração, do processo e dos servidores de destino mestre que usam ao configurar a recuperação de desastres de VMMs VMware no local para o Azure com o Azure Site Recovery
ms.topic: conceptual
ms.date: 03/17/2020
ms.openlocfilehash: cd5ded18d1a8f1f5fd96212d37725bb5db13002f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062086"
---
# <a name="about-site-recovery-components-configuration-process-master-target"></a>Sobre os componentes de recuperação de site (configuração, processo, destino mestre)

Este artigo descreve a configuração, o processo e os servidores-alvo mestre usados pelo serviço [de recuperação de sites](site-recovery-overview.md) para replicar VMware VMs e servidores físicos para o Azure.

## <a name="configuration-server"></a>Servidor de configuração

Para a recuperação de desastres de VMMs vMware e servidores físicos no local, implante um servidor de configuração de recuperação de local no local.

**Configuração** | **Detalhes** | **Links**
--- | --- | ---
**Componentes**  | A máquina do servidor de configuração executa todos os componentes de recuperação do site no local, que incluem o servidor de configuração, o servidor de processo e o servidor de destino mestre.<br/><br/> Quando você configura o servidor de configuração, todos os componentes são instalados automaticamente. | [Leia](vmware-azure-common-questions.md#configuration-server) o FAQ do servidor de configuração.
**Role** | O servidor de configuração coordena a comunicação entre o ambiente local e o Azure e gerencia a replicação de dados. | Saiba mais sobre a arquitetura para [VMware](vmware-azure-architecture.md) e recuperação de desastres [de servidor físico](physical-azure-architecture.md) para o Azure.
**Requisitos da VMware** | Para a recuperação de desastres de VMMs VMware no local, você deve instalar e executar o servidor de configuração como uma VMM VMis altamente disponível no local. | [Conheça](vmware-azure-deploy-configuration-server.md#prerequisites) os pré-requisitos.
**Implantação do VMware** | Recomendamos que você implante o servidor de configuração usando um modelo OVA baixado. Este método fornece uma maneira simples de configurar um servidor de configuração que esteja em conformidade com todos os requisitos e pré-requisitos.<br/><br/> Se por algum motivo você não conseguir implantar um VMware VMm usando um modelo OVA, você pode configurar as máquinas de servidor de configuração manualmente, conforme descrito abaixo para recuperação de desastres de máquinas físicas. | [Implantar](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template) com um modelo OVA.
**Requisitos de servidor físico** | Para recuperação de desastres em servidores físicos locais, você implanta o servidor de configuração manualmente. | [Conheça](physical-azure-set-up-source.md#prerequisites) os pré-requisitos.
**Implantação de servidor físico** | Se ele não puder ser instalado como uma VMware VMware, você pode instalá-lo em um servidor físico. | [Implante](physical-azure-set-up-source.md#set-up-the-source-environment) o servidor de configuração manualmente.

## <a name="process-server"></a>Servidor de processo

Um servidor de processo lida com dados de replicação durante failover e failback e instala o serviço Mobility para VMMs e servidores físicos VMware no local.

**Configuração** | **Detalhes** | **Links**
--- | --- | ---
**Implantação**  | Por padrão, quando o servidor de configuração é implantado, o servidor de processo é instalado. <br/><br/> Um servidor de processo no local é necessário para a recuperação de desastres e a replicação de VMMs e servidores físicos no local. | [Saiba mais](vmware-azure-architecture.md#architectural-components).
**Papel (no local)** | Recebe dados de replicação de máquinas habilitadas para replicação. <br/><br/> Otimiza os dados de replicação com cache, compactação e criptografia e os envia para o Azure Storage. <br/><br/> Realiza uma instalação push do serviço de mobilidade de recuperação do site em VMMs VMware e servidores físicos que você deseja replicar. <br/><br/> Realiza a descoberta automática de máquinas no local. | [Saiba mais](vmware-azure-enable-replication.md).
**Função (failback do Azure)** | Após o failover do seu site local, você configura um servidor de processo no Azure, como uma VM Azure, para lidar com o failback para o seu local de localização no local.<br/><br/> O servidor de processo no Azure é temporário. O Azure VM pode ser excluído após o failback ser feito. | [Saiba mais](vmware-azure-set-up-process-server-azure.md).
**Escala** | Para implantações maiores, no local você pode configurar servidores de processo adicionais de saída de escala. Servidores adicionais ampliam a capacidade, manuseando um número maior de máquinas replicantes e volumes maiores de tráfego de replicação.<br/><br/> Você pode mover máquinas entre dois servidores de processo, a fim de carregar o tráfego de replicação de equilíbrio. | [Saiba mais](vmware-azure-set-up-process-server-scale.md).

## <a name="master-target-server"></a>Servidor de destino mestre

O servidor de destino mestre lida com os dados de replicação durante o failback do Azure.

- Por padrão, o servidor de destino mestre é instalado no servidor de configuração.
- Para grandes implantações, você pode adicionar um servidor de destino mestre separado adicional para failback.

## <a name="next-steps"></a>Próximas etapas

- Revise a [arquitetura](vmware-azure-architecture.md) para recuperação de desastres de VMware VMs e servidores físicos.
- Revise os [requisitos e pré-requisitos](vmware-physical-azure-support-matrix.md) para recuperação de desastres de VMware VMware e servidores físicos para o Azure.
