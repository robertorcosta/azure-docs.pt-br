---
title: Faq de migração de servidor do Azure Migrate
description: Obtenha respostas para perguntas comuns sobre o uso do Azure Migrate Server Migration para migrar máquinas.
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: cc78d2087dcaad2922ca6b6d9c090a8decdb6e84
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393805"
---
# <a name="azure-migrate-server-migration-common-questions"></a>Migração do servidor Azure Migrate: perguntas comuns

Este artigo responde a perguntas comuns sobre a ferramenta Migração do Azure: Migração de servidor. Se você tiver outras perguntas, verifique esses recursos:

- [Perguntas gerais](resources-faq.md) sobre o Azure Migrate
- Perguntas sobre o [aparelho Azure Migrate](common-questions-appliance.md)
- Perguntas sobre [descoberta, avaliação e visualização de dependência](common-questions-discovery-assessment.md)
- Receba perguntas respondidas no [fórum Azure Migrate](https://aka.ms/AzureMigrateForum)

## <a name="how-does-agentless-vmware-replication-work"></a>Como funciona a replicação de VMware sem agente?

O método de replicação sem agente para VMware usa instantâneos VMware e VMware Changed Block Tracking (CBT).

Este é o processo:

1. Quando você inicia a replicação, um ciclo inicial de replicação é programado. No ciclo inicial, um instantâneo da VM é tirado. O snapshot é usado para replicar os VMs VMDKs (discos). 
2. Após o término do ciclo inicial de replicação, os ciclos de replicação delta são programados periodicamente.
    - Durante a replicação delta, um instantâneo é feito e os blocos de dados que foram alterados desde o ciclo de replicação anterior são replicados.
    - O VMware CBT é usado para determinar os blocos que mudaram desde o último ciclo.
    - A frequência dos ciclos de replicação periódica é gerenciada automaticamente pelo Azure Migrate e depende de quantas outras VMs e discos estão se replicando simultaneamente do mesmo datastore. Em condições ideais, a replicação eventualmente converge para um ciclo por hora para cada VM.

Quando você migra, um ciclo de replicação sob demanda é programado para que a máquina capture quaisquer dados restantes. Para garantir a perda zero de dados e a consistência do aplicativo, você pode optar por desligar a máquina durante a migração.

## <a name="why-isnt-resynchronization-exposed"></a>Por que a ressincronização não está exposta?

Durante a migração sem agente, em cada ciclo delta, a diferença entre o instantâneo atual e o instantâneo anteriormente tirado é escrita. É sempre a diferença entre instantâneos, dados dobráveis. Se um setor específico for escrito *N* vezes entre instantâneos, apenas a última gravação precisa ser transferida porque estamos interessados apenas na última sincronização. O processo é diferente da replicação baseada em agente, durante a qual rastreamos e aplicamos cada gravação. Nesse processo, cada ciclo delta é uma ressincronização. Então, nenhuma opção de ressincronização exposta. Se os discos não forem sincronizados por causa de uma falha, ele será corrigido no próximo ciclo. 

## <a name="how-does-churn-rate-affect-agentless-replication"></a>Como a taxa de churn afeta a replicação sem agente?

Como a replicação sem agente dobra nos dados, o *padrão de churn* é mais importante do que a taxa de *churn*. Quando um arquivo é escrito várias vezes, a taxa não tem muito impacto. No entanto, um padrão no qual todos os outros setores são escritos causa alta agitação no próximo ciclo. Como minimizamos a quantidade de dados que transferimos, permitimos que os dados dobrem o máximo possível antes de agendarmos o próximo ciclo.  

## <a name="how-frequently-is-a-replication-cycle-scheduled"></a>Com que freqüência um ciclo de replicação é programado?

A fórmula para agendar o próximo ciclo de replicação é (tempo de ciclo anterior / 2) ou uma hora, o que for maior.

Por exemplo, se um VM leva quatro horas para um ciclo delta, o próximo ciclo é programado em duas horas, e não na próxima hora. O processo é diferente imediatamente após a replicação inicial, quando o primeiro ciclo delta é programado imediatamente.

## <a name="how-does-agentless-replication-affect-vmware-servers"></a>Como a replicação sem agente afeta os servidores VMware?

A replicação sem agente resulta em algum impacto de desempenho nos hosts VMware vCenter Server e VMware ESXi. Como a replicação sem agente usa instantâneos, ele consome IOPS no armazenamento, de modo que alguma largura de banda de armazenamento IOPS é necessária. Não recomendamos o uso de replicação sem agente se você tiver restrições no armazenamento ou IOPs em seu ambiente.

## <a name="can-i-do-agentless-migration-of-uefi-vms-to-azure-gen-2"></a>Posso fazer a migração sem agente de VMs UEFI para o Azure Gen 2?

Não. Use a recuperação do site do Azure para migrar essas VMs para as VMs Da Gen 2 Azure. 

## <a name="can-i-pin-vms-to-azure-availability-zones-when-i-migrate"></a>Posso fixar VMs em Zonas de Disponibilidade do Azure quando migrar?

Não. As zonas de disponibilidade do Azure não são suportadas para migração do Azure Migrate.

## <a name="what-transport-protocol-does-azure-migrate-use-during-replication"></a>Que protocolo de transporte o Azure Migrate usa durante a replicação?

O Azure Migrate usa o protocolo NBD (Network Block Device) com criptografia SSL.

## <a name="what-is-the-minimum-vcenter-server-version-required-for-migration"></a>Qual é a versão mínima do vCenter Server necessária para migração?

Você deve ter pelo menos o host vCenter Server 5.5 e vSphere ESXi versão 5.5.

## <a name="can-customers-migrate-their-vms-to-unmanaged-disks"></a>Os clientes podem migrar suas VMs para discos não gerenciados?

Não. O Azure Migrate suporta migração apenas para discos gerenciados (HDD padrão, SSD Premium).

## <a name="how-many-vms-can-i-replicate-at-one-time-by-using-agentless-migration"></a>Quantas VMs posso replicar ao mesmo tempo usando migração sem agente?

Atualmente, você pode migrar 100 VMs por instância do vCenter Server simultaneamente. Migre em lotes de 10 VMs.

## <a name="how-do-i-throttle-replication-in-using-azure-migrate-appliance-for-agentless-vmware-replication"></a>Como faço para acelerar a replicação ao usar o aparelho Azure Migrate para replicação VMware sem agente?  

Você pode acelerar usando o NetQosPolicy. Por exemplo:

O AppNamePrefix a ser usado no NetQosPolicy é "GatewayWindowsService.exe". Você pode criar uma política no dispositivo Azure Migrate para acelerar o tráfego de replicação do aparelho, criando uma política como esta:
 
New-NetQosPolicy -Nome "ThrottleReplication" -AppPathNameMatchCondition "GatewayWindowsService.exe" -ThrottleRateActionBitsPerSecond 1MB

## <a name="when-do-i-migrate-machines-as-physical-servers"></a>Quando eu migrar máquinas como servidores físicos?

Migrar computadores tratando-os como servidores físicos é útil em vários cenários:

- Quando você está migrando servidores físicos no local.
- Se você está migrando VMs virtualizados por plataformas como Xen, KVM.
- Para migrar VMs Hyper-V ou VMware, se por algum motivo você não puder usar o processo de migração padrão para [migração Hyper-V](tutorial-migrate-hyper-v.md)ou [VMware.](server-migrate-overview.md) Por exemplo, se você não estiver executando o VMware vCenter e estiver usando apenas hosts ESXi.
- Para migrar VMs que estão atualmente em execução em nuvens privadas para o Azure
- Se você quiser migrar vMs em execução em nuvens públicas, como Amazon Web Services (AWS) ou Google Cloud Platform (GCP), para o Azure.

## <a name="i-deployed-two-or-more-appliances-to-discover-vms-in-my-vcenter-server-however-when-i-try-to-migrate-the-vms-i-only-see-vms-corresponding-to-one-of-the-appliance"></a>Implantei dois (ou mais) aparelhos para descobrir VMs no meu vCenter Server. No entanto, quando tento migrar as VMs, só vejo VMs correspondentes a um dos aparelhos.

Embora este possa ser um bom caso de uso, atualmente não o apoiamos. A implantação de dois (ou mais) aparelhos para descobrir o mesmo conjunto de VMs causa um problema de serviço no qual a propriedade da VM continua alternando entre os dois aparelhos. Esta é a razão pela qual você vê VMs aparecendo e desaparecendo. Nesses casos, para resolver o problema, você deve excluir um aparelho e fazer uma atualização difícil.

## <a name="do-i-need-vmware-vcenter-to-migrate-vmware-vms"></a>Preciso do VMware vCenter para migrar vMware VMware?
Para [migrar vMware VMware](server-migrate-overview.md) usando migração baseada em agente VMware ou sem agente, os hosts ESXi nos quais as VMs estão localizadas devem ser gerenciados pelo vCenter Server. Se você não tiver o vCenter Server, você pode migrar vMs VMware migrando-os como servidores físicos. [Saiba mais](migrate-support-matrix-physical-migration.md).
 
## <a name="next-steps"></a>Próximas etapas

Leia a visão geral do [Azure Migrate](migrate-services-overview.md).
