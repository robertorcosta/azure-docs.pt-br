---
title: Atualizações e atualizações de componentes na Recuperação do Site do Azure
description: Fornece uma visão geral das atualizações do serviço de recuperação do site do Azure e atualizações de componentes.
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: rajanaki
ms.openlocfilehash: 3489f7f812798504d0c57a265a04e57344105419
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257817"
---
# <a name="service-updates-in-site-recovery"></a>Atualizações de serviço na Recuperação de Sites

Este artigo fornece uma visão geral das atualizações do [Azure Site Recovery](site-recovery-overview.md) e descreve como atualizar os componentes de recuperação do site.

O Site Recovery publica atualizações de serviço regularmente. As atualizações incluem novos recursos, melhorias de suporte, atualizações de componentes e correções de bugs. Para aproveitar os recursos e correções mais recentes, recomendamos executar as versões mais recentes dos componentes do Site Recovery. 
 
 
## <a name="updates-support"></a>Suporte a atualizações

### <a name="support-statement-for-azure-site-recovery"></a>Declaração de suporte do Azure Site Recovery

Recomendamos sempre atualizar para as versões mais recentes dos componentes:

**A cada nova versão 'N' de um componente azure Site Recovery que é lançado, todas as versões abaixo de 'N-4' são consideradas sem suporte**. 

> [!IMPORTANT]
> O suporte oficial é para atualizar de > versão N-4 para n. Por exemplo, se você estiver executando você está no N-6, você precisa primeiro atualizar para N-4 e, em seguida, atualizar para N.


### <a name="links-to-currently-supported-update-rollups"></a>Links para pacotes cumulativos de atualizações atualmente compatíveis

 Revise o rollup de atualização mais recente (versão N) [neste artigo](site-recovery-whats-new.md). Lembre-se que o Site Recovery fornece suporte para versões N-4.



## <a name="component-expiry"></a>Expiração do componente

A Recuperação do Site notifica você de componentes vencidos (ou perto do vencimento) por e-mail (se você se inscreveu em notificações de e-mail), ou no painel do cofre no portal.

- Além disso, quando as atualizações estão disponíveis, na visualização de infra-estrutura para o seu cenário no portal, um botão **Atualizar disponível** aparece ao lado do componente. Este botão redireciona você para um link para baixar a versão mais recente do componente.
-  As notificações do painel de cofres não estão disponíveis se você estiver replicando VMs Hyper-V. 

As notificações de e-mails são enviadas da seguinte forma.

**Tempo** | **Freqüência**
--- | ---
60 dias antes do término do componente | Uma vez quinzenalmente
Próximos 53 dias | Uma vez por semana
Últimos 7 dias | Uma vez por dia
Após o término | Uma vez quinzenalmente


### <a name="upgrading-outside-official-support"></a>Atualização do suporte oficial externo

Se a diferença entre a versão do componente e a versão de versão mais recente for maior que quatro, isso será considerado fora do suporte. Neste caso, atualize da seguinte forma: 

1. Atualize o componente instalado no momento para a sua versão atual mais quatro. Por exemplo, se a sua versão se 9.16, então atualize para 9.20.
2. Em seguida, atualize para a próxima versão compatível. Então, em nosso exemplo, depois de atualizar 9.16 para 9.20, atualize para 9.24. 

Siga o mesmo processo para todos os componentes relevantes.

### <a name="support-for-latest-operating-systemskernels"></a>Suporte para sistemas/núcleos operacionais mais recentes

> [!NOTE]
> Se você tiver uma janela de manutenção agendada e uma reinicialização estiver incluída nela, recomendamos que você primeiro atualize os componentes de recuperação do site e, em seguida, prossiga com o resto das atividades programadas na janela de manutenção.

1. Antes de atualizar as versões do sistema operacional/kernel, verifique se a versão de destino é suportada em Recuperação de Site. 

    - [Suporte a Azure VM.](azure-to-azure-support-matrix.md#replicated-machine-operating-systems)
    - Suporte ao [VMware/servidor físico](vmware-physical-azure-support-matrix.md#replicated-machines)
    - [Suporte hiper-V.](hyper-v-azure-support-matrix.md#replicated-vms)
2. Revise [as atualizações disponíveis](site-recovery-whats-new.md) para descobrir o que você deseja atualizar.
3. Atualize para a versão mais recente do Site Recovery.
4. Atualize o sistema operacional/kernel para as versões necessárias.
5. Reinicialize.


Esse processo garante que o sistema operacional/kernel da máquina seja atualizado para a versão mais recente e que as alterações mais recentes de Recuperação de Site necessárias para suportar a nova versão sejam carregadas na máquina.

## <a name="azure-vm-disaster-recovery-to-azure"></a>Recuperação de desastre da VM do Azure para o Azure

Neste cenário, recomendamos fortemente que você [habilite atualizações automáticas.](azure-to-azure-autoupdate.md) Você pode permitir que a Recuperação do Site gerencie atualizações da seguinte forma:

- Durante o processo de replicação habilitada.
- Definindo as configurações de atualização de extensão dentro do cofre.

Se você quiser gerenciar manualmente as atualizações, faça o seguinte:

1. No cofre > **Itens Replicados,** clique nesta notificação na parte superior da tela: 
    
    **A nova atualização do agente de replicação do Site Recovery está disponível. Clique para instalar ->**

4. Selecione as VMs para as quais deseja aplicar a atualização e clique em **OK**.


## <a name="vmware-vmphysical-server-disaster-recovery-to-azure"></a>Recuperação de desastres do VMware VMware/servidor físico para o Azure

1. Com base na sua versão atual e na [instrução de suporte,](#support-statement-for-azure-site-recovery)instale a atualização primeiro no servidor de configuração local, usando [estas instruções](vmware-azure-deploy-configuration-server.md#upgrade-the-configuration-server). 
2. Se você tiver servidores de processo de escala, atualize-os em seguida, usando [estas instruções](vmware-azure-manage-process-server.md#upgrade-a-process-server).
3. Para atualizar o agente de mobilidade em cada máquina protegida, consulte [este](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal) artigo.

### <a name="reboot-after-mobility-service-upgrade"></a>Reinicializar após a atualização do serviço de mobilidade

Uma reinicialização é recomendada após cada atualização do serviço de mobilidade, para garantir que todas as últimas alterações sejam carregadas na máquina de origem.

Uma reinicialização não é obrigatória, a menos que a diferença entre a versão do agente durante a última reinicialização, e a versão atual, seja maior que quatro.

O exemplo na tabela mostra como isso funciona.

|**Versão do agente (última reinicialização)** | **Atualizar para** | **Reinicialização obrigatória?**|
|---------|---------|---------|
|9.16 |  9.18 | Não obrigatório|
|9.16 | 9.19 | Não obrigatório|
| 9.16 | 9.20 | Não obrigatório
 | 9.16 | 9.21 | Mandatory.<br/><br/> Atualize para 9.20 e, em seguida, reinicie antes de atualizar para 9.21.

## <a name="hyper-v-vm-disaster-recovery-to-azure"></a>Recuperação de desastre da VM do Hyper-V para o Azure

### <a name="between-a-hyper-v-site-and-azure"></a>Entre um site hyper-v e azure

1. Baixe a atualização para o Provedor de Recuperação do Site do Microsoft Azure.
2. Instale o Provedor em cada servidor Hyper-V registrado na Recuperação do Site. Se você estiver executando um cluster, atualize em todos os nós de cluster.


## <a name="between-an-on-premises-vmm-site-and-azure"></a>Entre um site do VMM local e o Azure
1. Baixe a atualização para o Provedor de Recuperação do Site do Microsoft Azure.
2. Instale o Provedor no servidor VMM. Se o VMM for implantado em um cluster, instale o Provedor em todos os nós de cluster.
3. Instale o mais recente agente microsoft azure recovery services em todos os hosts Hyper-V ou nós de cluster.


## <a name="between-two-on-premises-vmm-sites"></a>Entre dois sites do VMM locais
1. Baixe a última atualização para o Provedor de Recuperação do Site do Microsoft Azure.
2. Instale o provedor mais recente no servidor VMM que gerencia o local de recuperação secundário. Se o VMM for implantado em um cluster, instale o Provedor em todos os nós de cluster.
3. Depois que o site de recuperação for atualizado, instale o Provedor no servidor VMM que está gerenciando o site principal.

## <a name="next-steps"></a>Próximas etapas

Siga nossa página [Atualizações do Azure](https://azure.microsoft.com/updates/?product=site-recovery) para acompanhar novas atualizações e lançamentos.
