---
title: Perguntas comuns sobre a migração de servidor de migrações para Azure
description: Obtenha respostas para perguntas comuns sobre a migração de computadores com a migração de servidor de migrações para Azure
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 0c967027457b925b45ea19d994cfadfdbd0b8ab3
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2020
ms.locfileid: "77425826"
---
# <a name="azure-migrate-server-migration-common-questions"></a>Migração de servidor de migrações para Azure: perguntas comuns

Este artigo responde a perguntas comuns sobre o migrações para Azure: ferramenta de migração de servidor. Se você tiver mais dúvidas depois de ler este artigo, leia estes artigos:

- [Perguntas gerais](resources-faq.md) sobre as migrações para Azure.
- [Perguntas](common-questions-appliance.md) sobre o dispositivo migrações para Azure.
- [Perguntas](common-questions-discovery-assessment.md) sobre a descoberta, avaliação e visualização de dependência.
- Poste perguntas no [Fórum de migrações para Azure](https://aka.ms/AzureMigrateForum).


## <a name="how-does-agentless-vmware-replication-work"></a>Como funciona a replicação do VMware sem agente?

O método de replicação sem agente para VMware usa instantâneos do VMware e o CBT (controle de bloqueio) alterado pela VMware.

1. Quando você inicia a replicação, um ciclo de replicação inicial é agendado. No ciclo inicial, um instantâneo da VM é tirado. Esse instantâneo é usado para replicar os VMDK (discos) de VMs. 
2. Após a conclusão do ciclo de replicação inicial, os ciclos de replicação delta são agendados periodicamente.
    - Durante a replicação delta, um instantâneo é tirado e os blocos de dados que foram alterados desde o ciclo de replicação anterior são replicados.
    - O VMware CBT é usado para determinar os blocos que foram alterados desde o último ciclo.
    - A frequência dos ciclos de replicação periódicos é gerenciada automaticamente pelas migrações para Azure, dependendo de quantas outras VMs/discos estão replicando simultaneamente do mesmo repositório de armazenamento. Nas condições ideais, a replicação eventualmente é convergida para um ciclo por hora para cada VM.

Quando você migra, um ciclo de replicação sob demanda é agendado para a máquina para capturar os dados restantes. Você pode optar por desligar o computador durante a migração para garantir zero perda de dados e consistência do aplicativo.

## <a name="why-isnt-resynchronization-exposed"></a>Por que a ressincronização não está exposta?

Durante a migração sem agente, em cada ciclo Delta, a diferença entre o instantâneo atual e o instantâneo previamente criado é gravada. Já que é sempre a diferença entre instantâneos, dobrando dados em. Assim, se um setor específico for gravado N vezes entre os instantâneos, apenas a última gravação precisará ser transferida, pois estamos interessados apenas na última sincronização. Isso difere da replicação baseada em agente, onde acompanhamos e aplicamos cada gravação. Isso significa que cada ciclo Delta é uma ressincronização. Portanto, não há nenhuma opção de ressincronização exposta. Se os discos não estiverem sincronizados devido a uma falha, eles serão corrigidos no próximo ciclo. 

## <a name="how-does-churn-rate-impact-agentless-replication"></a>Como a taxa de rotatividade impacta a replicação sem agente?

Como a data dobra da replicação sem agente, o padrão de rotatividade é mais importante que a taxa de rotatividade. Quando um arquivo é gravado novamente e novamente, a taxa não tem muito impacto. No entanto, um padrão no qual cada outro setor é escrito causa alta rotatividade no próximo ciclo. Como minimizamos a quantidade de dados que transferimos, permitimos que os dados dobrem o máximo possível antes de agendar o próximo ciclo.  

## <a name="how-frequently-is-a-replication-cycle-scheduled"></a>Com que frequência um ciclo de replicação é agendado?

A fórmula para agendar o próximo ciclo de replicação: (tempo de ciclo anterior/2) ou 1 hora, o que for maior.

Por exemplo, se uma VM levar quatro horas para um ciclo Delta, o próximo ciclo será agendado em duas horas, e não na próxima hora. Isso é diferente imediatamente após a replicação inicial, em que o primeiro ciclo Delta é agendado imediatamente.

## <a name="how-does-agentless-replication-impact-vmware-servers"></a>Como a replicação sem agente afeta os servidores VMware?

Há algum impacto no desempenho em hosts vCenter Server/ESXi. Como a replicação sem agente usa instantâneos, ele consome IOPs no armazenamento e alguma largura de banda de armazenamento de IOPS é necessária. Não recomendamos o uso da replicação sem agente se houver restrições de armazenamento/IOPs em seu ambiente.

## <a name="can-i-do-agentless-migration-of-uefi-vms-to-azure-gen-2"></a>Posso fazer a migração sem agente de VMs UEFI para o Azure Gen 2?

Não. Use Azure Site Recovery para migrar essas VMs para VMs do Azure de Gen 2. 

## <a name="can-i-pin-vms-to-azure-availability-zones-when-i-migrate"></a>Posso fixar VMs para Zonas de Disponibilidade do Azure quando eu migrar?

Não, não há suporte para Zonas de Disponibilidade do Azure.

## <a name="which-transport-protocol-is-used-by-azure-migrate-during-replication"></a>Qual protocolo de transporte é usado pelas migrações para Azure durante a replicação?

As migrações para Azure usam o protocolo NBD (dispositivo de bloco de rede) com criptografia SSL.

## <a name="what-is-the-minimum-vcenter-server-version-required-for-migration"></a>Qual é a versão de vCenter Server mínima necessária para a migração?

Você precisa ter pelo menos vCenter Server 5,5 e VMware vSphere o host ESXi versão 5,5.

## <a name="can-customers-migrate-their-vms-to-unmanaged-disks"></a>Os clientes podem migrar suas VMs para discos não gerenciados?

Não. As migrações para Azure dão suporte à migração somente para discos gerenciados (HDD padrão, SSD Premium).

## <a name="how-many-vms-can-i-replicate-together-with-agentless-migration"></a>Quantas VMs posso replicar junto com a migração sem agente?

No momento, você pode migrar 100 VMs por vCenter Server simultaneamente. Migre em lotes de 10 VMs.
 



