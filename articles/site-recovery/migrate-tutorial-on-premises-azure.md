---
title: Migrar computadores locais com o Azure Site Recovery
description: Este artigo descreve como migrar máquinas locais para o Azure, usando o Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: ccf83bacedb667e52e9865b6d451641faa0ac414
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86131189"
---
# <a name="migrate-on-premises-machines-to-azure"></a>Migrar máquinas locais para o Azure


Este artigo descreve como migrar máquinas locais para o Azure, usando o [Azure Site Recovery](site-recovery-overview.md). 

> [!TIP]
> Agora você deve usar Migrações para Azure para migrar computadores locais para o Azure, em vez do serviço Azure Site Recovery. [Saiba mais](../migrate/migrate-services-overview.md).


Este tutorial mostra como migrar VMs locais e servidores físicos para o Azure. Você aprenderá como:

> [!div class="checklist"]
> * Configurar os ambientes de origem e destino para migração
> * Configurar uma política de replicação
> * Habilitar a replicação
> * Execute uma migração de teste para verificar se tudo está funcionando como esperado
> * Executar um failover avulso para o Azure


> [!TIP]
> Agora você pode migrar os servidores locais para o Azure usando o serviço Migrações para Azure. [Saiba mais](../migrate/migrate-services-overview.md)

## <a name="before-you-start"></a>Antes de começar

Não há suporte para dispositivos exportados por drivers paravirtualizados.


## <a name="prepare-azure-and-on-premises"></a>Preparar o Azure e o local

1. Prepare o Azure conforme descrito [neste artigo](tutorial-prepare-azure.md). Embora este artigo descreva as etapas de preparação para recuperação de desastres, as etapas também são válidas para migração.
2. Preparar servidores [VMware](vmware-azure-tutorial-prepare-on-premises.md) ou [Hyper-V](hyper-v-prepare-on-premises-tutorial.md) locais. Se você estiver migrando computadores físicos, não precisará preparar nada. Apenas verifique a [matriz de suporte](vmware-physical-azure-support-matrix.md).


## <a name="select-a-protection-goal"></a>Selecionar uma meta de proteção

Selecione o que você deseja replicar e para onde deseja replicar.
1. Clique em **Cofres dos Serviços de Recuperação** > cofre.
2. No Menu Recursos, clique em **Site Recovery** > **Preparar Infraestrutura** > **Meta de proteção**.
3. Em **Objetivo de proteção**, selecione o que você deseja migrar.
    - **VMware**: selecione **Para o Azure** > **Sim, com o VMWare vSphere Hypervisor**.
    - **Computador físico**: selecione **Para o Azure** > **Não virtualizado/outro**.
    - **Hyper-V**: selecione **Para o Azure** > **Sim, com o Hyper-V**. Se as VMs do Hyper-V são gerenciadas pelo VMM, selecione **Sim**.


## <a name="set-up-the-source-environment"></a>Configurar o ambiente de origem

**Cenário** | **Detalhes**
--- | --- 
VMware | Configure o [ambiente de origem](vmware-azure-set-up-source.md) e o [servidor de configuração](vmware-azure-deploy-configuration-server.md).
Computador físico | [Configure](physical-azure-set-up-source.md) o ambiente de origem e o servidor de configuração.
Hyper-v | Configurar o [ambiente de origem](hyper-v-azure-tutorial.md#set-up-the-source-environment)<br/><br/> Configure o [ambiente de origem](hyper-v-vmm-azure-tutorial.md#set-up-the-source-environment) para Hyper-V implantado com o System Center VMM.

## <a name="set-up-the-target-environment"></a>Configurar o ambiente de origem

Selecione e verifique os recursos de destino.

1. Clique em **Preparar infraestrutura** > **Destino** e selecione a assinatura do Azure que você deseja usar.
2. Especifique o modelo de implantação do Gerenciador de Recursos.
3. O Site Recovery verifica os recursos do Azure.
    - Se você estiver migrando VMs VMware ou servidores físicos, o Site Recovery verificará se você tem uma rede do Azure na qual as VMs do Azure estarão localizadas ao serem criadas após o failover.
    - Se você estiver migrando VMs do Hyper-V, o Site Recovery verificará se você tem uma rede e uma conta de armazenamento do Azure compatíveis.
4. Se você estiver migrando VMs do Hyper-V gerenciadas pelo System Center VMM, configure [mapeamento de rede](hyper-v-vmm-azure-tutorial.md#configure-network-mapping).

## <a name="set-up-a-replication-policy"></a>Configurar uma política de replicação

**Cenário** | **Detalhes**
--- | --- 
VMware | Configurar uma [política de replicação](vmware-azure-set-up-replication.md) para VMs VMware.
Computador físico | Configurar uma [política de replicação](physical-azure-disaster-recovery.md#create-a-replication-policy) para computadores físicos.
Hyper-v | Configurar uma [política de replicação](hyper-v-azure-tutorial.md#set-up-a-replication-policy)<br/><br/> Configurar uma [política de replicação](hyper-v-vmm-azure-tutorial.md#set-up-a-replication-policy) para Hyper-V implantado com o VMM do System Center.

## <a name="enable-replication"></a>Habilitar a replicação

**Cenário** | **Detalhes**
--- | --- 
VMware | [Habilite a replicação](vmware-azure-enable-replication.md) para VMs VMware.
Computador físico | [Habilite a replicação](physical-azure-disaster-recovery.md#enable-replication) para computadores físicos.
Hyper-v | [Habilitar a replicação](hyper-v-azure-tutorial.md#enable-replication)<br/><br/> [Habilite a replicação](hyper-v-vmm-azure-tutorial.md#enable-replication) para Hyper-V implantado com o System Center VMM.


## <a name="run-a-test-migration"></a>Execute um teste de migração

Execute um [failover de teste](tutorial-dr-drill-azure.md) para o Azure, para verificar se tudo está funcionando conforme o esperado.


## <a name="migrate-to-azure"></a>Migrar para o Azure

Execute um failover para as máquinas que você deseja migrar.

1. Em **Configurações** > **Itens replicados** clique no computador > **Failover**.
2. Em **Failover**, selecione um **Ponto de Recuperação** para executar o failover. Selecione o ponto de recuperação mais recente.
3. A configuração de chave de criptografia não é relevante para esse cenário.
4. Selecione **Desligar o computador antes do início do failover**. O Site Recovery tentará desligar máquinas virtuais antes de disparar o failover. O failover continuará mesmo o desligamento falhar. Você pode acompanhar o progresso do failover na página **Trabalhos** .
5. Verifique se a VM do Azure aparece no Azure, conforme o esperado.
6. Em **Itens replicados**, clique com o botão direito do mouse em VM > **Concluir Migração**. Isso faz o seguinte:

   - Conclui o processo de migração, interrompe a replicação da VM local e interrompe a cobrança do Site Recovery para a VM.
   - Esta etapa limpa os dados de replicação. Ela não exclui as VMs migradas.

     ![Migração completa](./media/migrate-tutorial-on-premises-azure/complete-migration.png)


> [!WARNING]
> **Não cancele um failover em andamento**: a replicação da VM é interrompida antes do início do failover. Se você cancelar um failover em andamento, o failover será interrompido, mas a VM não será replicada novamente.

Em alguns cenários, o failover requer um processamento adicional que leva cerca de oito a dez minutos para ser concluído. Você pode observar tempos de failover de teste mais longos para servidores físicos, computadores VMware Linux, VMs do VMware que não têm o serviço DHCP habilitado e VMs do VMware que não têm os seguintes drivers de inicialização: storvsc, vmbus, storflt, intelide e atapi.

## <a name="after-migration"></a>Após a migração

Depois que as máquinas são migradas para o Azure, há uma série de etapas que devem ser concluídas.

Algumas etapas podem ser automatizadas como parte do processo de migração usando a funcionalidade dos scripts de automação internos nos [planos de recuperação](site-recovery-runbook-automation.md)   


### <a name="post-migration-steps-in-azure"></a>Etapas pós-migração no Azure

- Execute todos os ajustes no aplicativo após a migração, como atualizar as cadeias de conexão de banco de dados e as configurações do servidor Web. 
- Execute o aplicativo final e o teste de aceitação da migração no aplicativo migrado que está sendo executado no Azure.
- O [agente de VM do Azure](../virtual-machines/extensions/agent-windows.md) gerencia a interação entre uma VM do Azure e o controlador de malha do Azure. É necessário para alguns serviços do Azure, como o Backup do Azure, Site Recovery e Segurança do Azure.
    - Se você estiver migrando máquinas VMware e servidores físicos, o instalador do Serviço de Mobilidade instalará o agente de VM do Azure disponível nos computadores Windows. Em VMs do Linux, é recomendável instalar o agente após o failover.
    - Se você estiver migrando VMs do Azure para uma região secundária, o agente de VM do Azure precisará ser provisionado na VM antes da migração.
    - Se você estiver migrando VMs Hyper-V para o Azure, instale o agente de VM do Azure na VM do Azure após a migração.
- Remova manualmente todos os provedores/agentes do Site Recovery da VM. Se você migrar VMs VMware ou servidores físicos, desinstale o Serviço de mobilidade do VM.
- Para aumentar a resiliência:
    - Proteja os dados fazendo backup das VMs do Azure por meio do serviço Backup do Azure. [Saiba mais](../backup/quick-backup-vm-portal.md).
    - Mantenha as cargas de trabalho em execução e continuamente disponíveis ao replicar as VMs do Azure em uma região secundária com o Site Recovery. [Saiba mais](azure-to-azure-quickstart.md).
- Para aumentar a segurança:
    - Bloqueie e limite o acesso ao tráfego de entrada com a Central de Segurança do Azure [Administração just-in-time](../security-center/security-center-just-in-time.md)
    - Restrinja o tráfego de rede a pontos de extremidade com os [Grupos de Segurança de Rede](../virtual-network/security-overview.md).
    - Implante o [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md) para manter os discos em segurança e proteger os dados contra roubo e acesso não autorizado.
    - Leia mais sobre [como proteger recursos IaaS]( https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/ ) e acesse a [Central de Segurança do Azure](https://azure.microsoft.com/services/security-center/ ).
- Para monitoramento e gerenciamento:
    - Considere implantar o [Gerenciamento de Custos do Azure](../cost-management-billing/cloudyn/overview.md) para monitorar o uso de recursos e os gastos.

### <a name="post-migration-steps-on-premises"></a>Etapas locais após a migração

- Mova o tráfego do aplicativo para o aplicativo em execução na instância migrada da VM do Azure.
- Remova as VMs locais do inventário local de VMs.
- Remova as VMs locais dos backups locais.
- Atualize todas as documentações internas para mostrar o novo local e o endereço IP das VMs do Azure.




## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você migrou máquinas virtuais locais para máquinas virtuais do Azure. Agora

> [!div class="nextstepaction"]
> [Configurar a recuperação de desastre](azure-to-azure-replicate-after-migration.md) para uma região secundária do Azure para as VMs do Azure.

  
