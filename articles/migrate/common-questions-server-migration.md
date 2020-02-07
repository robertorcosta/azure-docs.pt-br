---
title: Perguntas comuns sobre a migração de servidor de migrações para Azure
description: Obtenha respostas para perguntas comuns sobre migração de servidor de migrações para Azure
ms.topic: conceptual
ms.date: 02/06/2020
ms.openlocfilehash: bae3447f0fada18de5473e1ef1a1c1d431535f63
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2020
ms.locfileid: "77067377"
---
# <a name="azure-migrate-server-migration-common-questions"></a>Migração de servidor de migrações para Azure: perguntas comuns

Este artigo responde a perguntas comuns sobre o migrações para Azure: migração de servidor. Se você tiver outras consultas depois de ler este artigo, poste-as no [Fórum de migrações para Azure](https://aka.ms/AzureMigrateForum). Se você tiver outras dúvidas, leia estes artigos:

- [Perguntas gerais](resources-faq.md) sobre as migrações para Azure.
- [Perguntas](common-questions-appliance.md) sobre o dispositivo migrações para Azure.
- [Perguntas](common-questions-discovery-assessment.md) sobre a descoberta, avaliação e visualização de dependência.


## <a name="how-does-agentless-vmware-replication-work"></a>Como funciona a replicação do VMware sem agente?

O método de replicação sem agente para VMware usa instantâneos do VMware e o CBT (controle de bloqueio) alterado pela VMware. Um ciclo de replicação inicial é agendado quando o usuário inicia a replicação. No ciclo de replicação inicial, um instantâneo da VM é tirado e esse instantâneo é usado para replicar os VMDK (discos) de VMs. Após a conclusão do ciclo de replicação inicial, os ciclos de replicação delta são agendados periodicamente. No ciclo de replicação delta, um instantâneo é tirado e os blocos de dados que foram alterados desde o ciclo de replicação anterior são replicados. O VMware Changed Monitoring Block é usado para determinar os blocos que foram alterados desde o último ciclo.
A frequência dos ciclos de replicação periódicos é gerenciada automaticamente pelo serviço, dependendo de quantas outras VMs/discos estão replicando simultaneamente o mesmo repositório de armazenamento e, em condições ideais, eventualmente convergirá para um ciclo por hora por VM.

Quando você migra, um ciclo de replicação sob demanda é agendado para a VM capturar os dados restantes. Você pode optar por desligar a VM como parte da migração para garantir zero perda de dados e consistência do aplicativo.

## <a name="why-is-the-resynchronization-option-not-exposed-in-agentless-stack"></a>Por que a opção de ressincronização não é exposta na pilha sem agente?

Na pilha sem agente, em cada ciclo Delta, transferimos a comparação entre o instantâneo atual e o instantâneo anterior que tínhamos feito. Como sempre é uma comparação entre os instantâneos, isso dá a vantagem de dobrar os dados (ou seja, se um setor específico é escrito ' n' vezes entre os instantâneos, só precisamos transferir a última gravação, pois estamos interessados apenas na última sincronização). Isso é diferente da pilha baseada em agente na qual acompanhamos todas as gravações e as aplicamos. Isso significa que cada ciclo Delta é uma ressincronização. Portanto, não há nenhuma opção de ressincronização exposta. 

Se os discos estiverem fora de sincronia devido a uma falha, eles serão corrigidos no próximo ciclo. 

## <a name="what-is-the-impact-of-churn-rate-if-i-use-agentless-replication"></a>Qual é o impacto da taxa de variação se eu usar a replicação sem agente?

Como a pilha depende dos dados que estão sendo dobrados, mais do que a taxa de rotatividade, o padrão de rotatividade é o que importa nessa pilha. Um padrão em que um arquivo está sendo gravado novamente não tem muito impacto. No entanto, um padrão no qual cada outro setor é escrito causará alta rotatividade no próximo ciclo. Como minimizamos a quantidade de dados que transferimos, permitimos que os dados dobrem o máximo possível antes de agendar o próximo ciclo.  

## <a name="how-frequently-is-a-replication-cycle-scheduled"></a>Com que frequência um ciclo de replicação é agendado?

A fórmula para agendar o próximo ciclo de replicação é esta: (tempo de ciclo anterior/2) ou 1 hora, o que for maior. Por exemplo, se uma VM levou quatro horas para um ciclo Delta, agendaremos seu próximo ciclo em 2 horas e não na próxima hora. Isso é diferente quando o ciclo é imediatamente após IR, onde agendamos o primeiro ciclo Delta imediatamente.

## <a name="what-is-the-impact-on-performance-of-vcenter-server-or-esxi-host-while-using-agentless-replication"></a>Qual é o impacto no desempenho de vCenter Server ou host ESXi ao usar a replicação sem agente?

Como a replicação sem agente usa instantâneos, haverá o consumo de IOPs no armazenamento e os clientes precisarão de algum espaço de IOPs no armazenamento. Não é recomendável usar essa pilha no ambiente de armazenamento/IOPs restrito.

## <a name="does-agentless-migration-stack-support-migration-of-uefi-vms-to-azure-gen-2-vms"></a>A pilha de migração sem agente dá suporte à migração de VMs UEFI para VMs do Azure Gen 2?

Não, você deve usar Azure Site Recovery para migrar essas VMs para VMs do Azure de Gen 2. 

## <a name="can-i-pin-my-vms-to-azure-availability-zones-when-i-migrate"></a>Posso fixar minhas VMs para Zonas de Disponibilidade do Azure quando eu migrar?

Não, o suporte para Zonas de Disponibilidade do Azure não está lá.

## <a name="which-transport-protocol-is-used-by-azure-migrate-during-replication"></a>Qual protocolo de transporte é usado pelas migrações para Azure durante a replicação?

As migrações para Azure usam o protocolo NBD (dispositivo de bloco de rede) com criptografia SSL.

## <a name="what-is-the-minimum-vcenter-server-version-required-for-migration"></a>Qual é a versão de vCenter Server mínima necessária para a migração?

Você precisa ter pelo menos vCenter Server 5,5 e VMware vSphere o host ESXi versão 5,5.

## <a name="can-customers-migrate-their-vms-to-unmanaged-disks"></a>Os clientes podem migrar suas VMs para discos não gerenciados?

Não. As migrações para Azure dão suporte à migração somente para discos gerenciados (HDD padrão, SSD Premium).

## <a name="how-many-vms-can-replicate-simultaneously-using-agentless-vmware-stack"></a>Quantas VMs podem ser replicadas simultaneamente usando o VMware Stack sem agente?

Atualmente, os clientes podem migrar 100 VMs por vCenter Server simultaneamente. Isso pode ser feito em lotes de 10 VMs.




