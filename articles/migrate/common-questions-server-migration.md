---
title: Perguntas frequentes sobre migração de servidor do Azure Migrate
description: Obtenha respostas para perguntas comuns sobre como usar a migração de servidor de migração do Azure para migrar computadores.
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 273ef746e685afcf9f3654963dd9c6bd5b855b24
ms.sourcegitcommit: 9cbd5b790299f080a64bab332bb031543c2de160
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2020
ms.locfileid: "78927526"
---
# <a name="azure-migrate-server-migration-common-questions"></a>Migração de servidor de migrações para Azure: perguntas comuns

Este artigo responde a perguntas comuns sobre o migrações para Azure: ferramenta de migração de servidor. Se você tiver outras dúvidas, verifique estes recursos:

- [Perguntas gerais](resources-faq.md) sobre as migrações para Azure
- Perguntas sobre o [dispositivo migrações para Azure](common-questions-appliance.md)
- Perguntas sobre [descoberta, avaliação e visualização de dependência](common-questions-discovery-assessment.md)
- Obter perguntas respondidas no [Fórum de migrações para Azure](https://aka.ms/AzureMigrateForum)

## <a name="how-does-agentless-vmware-replication-work"></a>Como funciona a replicação do VMware sem agente?

O método de replicação sem agente para VMware usa instantâneos do VMware e o CBT (controle de bloqueio alterado) VMware.

Este é o processo:

1. Quando você inicia a replicação, um ciclo de replicação inicial é agendado. No ciclo inicial, um instantâneo da VM é tirado. O instantâneo é usado para replicar os VMDK (discos) de VMs. 
2. Após a conclusão do ciclo de replicação inicial, os ciclos de replicação delta são agendados periodicamente.
    - Durante a replicação delta, um instantâneo é tirado e os blocos de dados que foram alterados desde o ciclo de replicação anterior são replicados.
    - O VMware CBT é usado para determinar os blocos que foram alterados desde o último ciclo.
    - A frequência dos ciclos de replicação periódicos é gerenciada automaticamente pelas migrações para Azure e depende de quantas outras VMs e discos estão replicando simultaneamente do mesmo repositório de armazenamento. Nas condições ideais, a replicação eventualmente é convergida para um ciclo por hora para cada VM.

Quando você migra, um ciclo de replicação sob demanda é agendado para a máquina capturar todos os dados restantes. Para garantir nenhuma perda de dados e consistência de aplicativos, você pode optar por desligar o computador durante a migração.

## <a name="why-isnt-resynchronization-exposed"></a>Por que a ressincronização não está exposta?

Durante a migração sem agente, em cada ciclo Delta, a diferença entre o instantâneo atual e o instantâneo previamente criado é gravada. É sempre a diferença entre instantâneos, dobrando dados em. Se um setor específico for gravado *N* vezes entre instantâneos, somente a última gravação precisará ser transferida porque estamos interessados apenas na última sincronização. O processo é diferente da replicação baseada em agente, durante o qual acompanhamos e aplicamos cada gravação. Nesse processo, cada ciclo Delta é uma ressincronização. Portanto, nenhuma opção de ressincronização é exposta. Se os discos nunca estiverem sincronizados devido a uma falha, eles serão corrigidos no próximo ciclo. 

## <a name="how-does-churn-rate-affect-agentless-replication"></a>Como a taxa de rotatividade afeta a replicação sem agente?

Como a replicação sem agente dobra em dados, o *padrão de rotatividade* é mais importante do que a *taxa de rotatividade*. Quando um arquivo é gravado novamente e novamente, a taxa não tem muito impacto. No entanto, um padrão no qual cada outro setor é escrito causa alta rotatividade no próximo ciclo. Como minimizamos a quantidade de dados que transferimos, permitimos que os dados dobrem o máximo possível antes de agendarmos o próximo ciclo.  

## <a name="how-frequently-is-a-replication-cycle-scheduled"></a>Com que frequência um ciclo de replicação é agendado?

A fórmula para agendar o próximo ciclo de replicação é (tempo de ciclo anterior/2) ou uma hora, o que for maior.

Por exemplo, se uma VM levar quatro horas para um ciclo Delta, o próximo ciclo será agendado em duas horas, e não na próxima hora. O processo é diferente imediatamente após a replicação inicial, quando o primeiro ciclo Delta é agendado imediatamente.

## <a name="how-does-agentless-replication-affect-vmware-servers"></a>Como a replicação sem agente afeta os servidores VMware?

A replicação sem agente resulta em algum impacto no desempenho em hosts VMware vCenter Server e VMware ESXi. Como a replicação sem agente usa instantâneos, ela consome IOPS no armazenamento, portanto, é necessária alguma largura de banda de armazenamento de IOPS. Não recomendamos o uso da replicação sem agente se você tiver restrições de armazenamento ou IOPs em seu ambiente.

## <a name="can-i-do-agentless-migration-of-uefi-vms-to-azure-gen-2"></a>Posso fazer a migração sem agente de VMs UEFI para o Azure Gen 2?

Não. Use Azure Site Recovery para migrar essas VMs para VMs do Azure de Gen 2. 

## <a name="can-i-pin-vms-to-azure-availability-zones-when-i-migrate"></a>Posso fixar VMs para Zonas de Disponibilidade do Azure quando eu migrar?

Não. Não há suporte para Zonas de Disponibilidade do Azure para migração de migrações para Azure.

## <a name="what-transport-protocol-does-azure-migrate-use-during-replication"></a>Qual protocolo de transporte faz a migração do Azure usar durante a replicação?

As migrações para Azure usam o protocolo NBD (dispositivo de bloco de rede) com criptografia SSL.

## <a name="what-is-the-minimum-vcenter-server-version-required-for-migration"></a>Qual é a versão de vCenter Server mínima necessária para a migração?

Você deve ter pelo menos vCenter Server 5,5 e o host ESXi vSphere versão 5,5.

## <a name="can-customers-migrate-their-vms-to-unmanaged-disks"></a>Os clientes podem migrar suas VMs para discos não gerenciados?

Não. As migrações para Azure dão suporte à migração somente para discos gerenciados (HDD Standard, SSD Premium).

## <a name="how-many-vms-can-i-replicate-at-one-time-by-using-agentless-migration"></a>Quantas VMs posso replicar ao mesmo tempo usando a migração sem agente?

No momento, você pode migrar 100 VMs por instância do vCenter Server simultaneamente. Migre em lotes de 10 VMs.
 
## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

Leia a [visão geral de migrações para Azure](migrate-services-overview.md).
