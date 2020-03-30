---
title: Reproteja as VMs do Azure para a região principal com a recuperação do site do Azure | Microsoft Docs
description: Descreve como reproteger as VMs do Azure após o failover, a região secundária à principal, usando o Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: 73747b8331054cdc3bfa1f4073ccf2cdb62ab326
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283235"
---
# <a name="reprotect-failed-over-azure-vms-to-the-primary-region"></a>Proteger novamente VMs do Azure que sofreram failover para a região primária

Quando você [falha sobre](site-recovery-failover.md) As VMs do Azure de uma região para outra usando [o Azure Site Recovery](site-recovery-overview.md), as VMs inicializam na região secundária, em um estado **desprotegido.** Se você quiser refazer as VMs para a região primária, faça as seguintes tarefas:

1. Proteja novamente as VMs na região secundária, para que elas iniciem a replicação para a região primária.
1. Depois que a reproteção for concluída e as VMs estiverem se replicando, você pode falhar da região secundária para a primária.

## <a name="prerequisites"></a>Pré-requisitos

- O failover de VM da região primária para a secundária deve ser confirmado.
- O local de destino primário deve estar disponível e você deve ser capaz de acessar ou criar recursos nessa região.

## <a name="reprotect-a-vm"></a>Proteger novamente uma VM

1. Em**Itens replicados do** **Vault,** > clique com o botão direito do mouse sobre a falha na VM e selecione **Re-Proteger**. A direção de nova proteção deve mostrar da secundária para a primária.

   ![Proteger novamente](./media/site-recovery-how-to-reprotect-azure-to-azure/reprotect.png)

1. Examine o grupo de recursos, a rede, o armazenamento e os conjuntos de disponibilidade. Em seguida, clique em **OK**. Se houver recursos marcados como novos, eles são criados como parte do processo de reproteção.
1. O trabalho de nova proteção propaga os dados mais recentes para o site de destino. Após o término do trabalho, a replicação delta acontece. Em seguida, você pode fazer failover de volta para o site primário. Você pode selecionar a conta de armazenamento ou a rede que você deseja usar ao proteger novamente, usando a opção personalizar.

   ![Opção de personalização](./media/site-recovery-how-to-reprotect-azure-to-azure/customize.png)

### <a name="customize-reprotect-settings"></a>Personalizar as configurações de nova proteção

Você pode personalizar as seguintes propriedades da VM de destino durante a reproteção.

![Personalizar](./media/site-recovery-how-to-reprotect-azure-to-azure/customizeblade.png)

|Propriedade |Observações  |
|---------|---------|
|Grupo de recursos de destino | Modifique o grupo de recursos de destino no qual a VM é criada. Como parte da nova proteção, a VM de destino é excluída. Você pode escolher um novo grupo de recursos sob o qual criar a VM após o failover. |
|Rede virtual de destino | A rede de destino não pode ser alterada durante o trabalho de nova proteção. Para alterar a rede, refaça o mapeamento de rede. |
|Armazenamento de destino (a VM secundária não usa discos gerenciados) | Você pode alterar a conta de armazenamento que a VM usa após o failover. |
|Discos gerenciados de réplica (a VM secundária usa discos gerenciados) | O Site Recovery cria discos gerenciados de réplica na região primária para espelhar discos gerenciados da VM secundária. |
|Armazenamento em cache | Você pode especificar uma conta de armazenamento de cache a ser usada durante a replicação. Por padrão, uma nova conta de armazenamento de cache é criada, se ela não existir. |
|Conjunto de disponibilidade | Se a VM na região secundária for parte de um conjunto de disponibilidade, você poderá escolher um conjunto de disponibilidade para a VM de destino na região primária. Por padrão, o Site Recovery tenta localizar o conjunto de disponibilidade existente na região primária e usá-lo. Durante a personalização, você pode especificar um novo conjunto de disponibilidade. |

### <a name="what-happens-during-reprotection"></a>O que acontece ao proteger novamente?

Por padrão, ocorre o seguinte:

1. Uma conta de armazenamento de cache é criada na região onde a VM com failover está em execução.
1. Se a conta de armazenamento de destino (a conta de armazenamento original na região primária) não existir, uma nova será criada. O nome da conta de armazenamento atribuído é o nome da conta de `asr`armazenamento usada pela VM secundária, sufixada com .
1. Se a sua VM usar discos gerenciados, os discos gerenciados de réplica serão criados na região primária para armazenar os dados replicados dos discos da VM secundária.
1. Se o conjunto de disponibilidade de destino não existir, um novo será criado como parte do trabalho de reproteger, se necessário. Se você personalizou as configurações de reproteção, o conjunto selecionado será usado.

Quando você dispara um trabalho de nova proteção e a VM de destino existe, ocorre o seguinte:

1. A VM do lado de destino é desligada quando ela está em execução.
1. Se a VM estiver usando discos gerenciados, uma cópia do `-ASRReplica` disco original será criada com um sufixo. Os discos originais são excluídos. As `-ASRReplica` cópias são usadas para replicação.
1. Se a VM estiver usando discos não gerenciados, os discos de dados da VM de destino são desanexados e usados para replicação. Uma cópia do disco do sistema operacional é criada e anexada na VM. O disco do sistema operacional original é desanexado e usado para replicação.
1. Somente alterações entre o disco de origem e o disco de destino são sincronizadas. Os diferenciais são computados comparando ambos os discos e, em seguida, transferidos. Confira abaixo o tempo estimado para concluir a reproteção.
1. Após a sincronização ser concluída, a replicação delta começa e um ponto de recuperação é criado de acordo com a política de replicação.

Quando você aciona um trabalho de reproteção, e as VM e discos de destino não existem, ocorre o seguinte:

1. Se a VM estiver usando discos gerenciados, os `-ASRReplica` discos de réplica serão criados com sufixo. As `-ASRReplica` cópias são usadas para replicação.
1. Se a VM estiver usando discos não gerenciados, réplicas dos discos originais serão criadas na conta de armazenamento de destino.
1. Todos discos são copiados da região com failover para a nova região de destino.
1. Após a sincronização ser concluída, a replicação delta começa e um ponto de recuperação é criado de acordo com a política de replicação.

#### <a name="estimated-time-to-do-the-reprotection"></a>Tempo estimado para fazer a reproteção

Na maioria dos casos, o Azure Site Recovery não replica os dados completos na região de origem.
As seguintes condições determinam a quantidade de dados replicados:

1. Se os dados de VM de origem forem excluídos, corrompidos ou inacessíveis por algum motivo, como uma alteração/exclusão de grupo de recursos, então durante a reproteção uma replicação inicial completa acontecerá porque não há dados disponíveis na região de origem para usar.
1. Se os dados de VM de origem estiverem acessíveis, então apenas os diferenciais serão calculados comparando os dois discos e depois transferidos. Confira a tabela abaixo para obter o tempo estimado.

|Situação de exemplo | Tempo levado para reproteger |
|---|---|
|A região de origem tem 1 VM com disco padrão de 1 TB.<br/>Apenas dados de 127 GB são usados, e o resto do disco está vazio.<br/>O tipo de disco é padrão com 60 MiB/S throughput.<br/>Nenhum dado muda após o failover.| Tempo aproximado: 45 minutos – 1,5 horas.<br/>Durante a reproteção, a Recuperação do Site preencherá a soma de verificação de todos os dados que levarão 127 GB/ 45 MBs, aproximadamente 45 minutos.<br/>Algum tempo de sobrecarga é necessário para que a recuperação do local seja dimensionada automaticamente, aproximadamente 20-30 minutos.<br/>Sem acusações de Egress. |
|A região de origem tem 1 VM com disco padrão de 1 TB.<br/>Apenas dados de 127 GB são usados e o resto do disco está vazio.<br/>O tipo de disco é padrão com 60 MiB/S throughput.<br/>45 GB de dados alterados após failover.| Tempo aproximado: 1 hora a 2 horas.<br/>Durante a reproteção, a Recuperação do Site preencherá a soma de verificação de todos os dados que levarão 127 GB/ 45 MBs, aproximadamente 45 minutos.<br/>Tempo de transferência para aplicar alterações de 45 GB que é de 45 GB/ 45 MBps, aproximadamente 17 minutos.<br/>As taxas de egresso seriam para alterações de dados de 45 GB, não para a soma de cheques. |

Quando a VM é reprotegida após falhar na região primária (ou seja, se a VM for reprotegida da região primária para a região DR), a VM de destino e as NIC(s) associadas serão excluídas.

Quando a VM é reprotegida da região DR para a região primária, não excluímos o VM primário e as NIC(s) associadas.

## <a name="next-steps"></a>Próximas etapas

Depois que a VM estiver protegida, você poderá iniciar um failover. O failover desliga o VM na região secundária e cria e inicializa a VM na região primária, com breve tempo de inatividade durante esse processo. Recomendamos que você escolha um momento apropriado para este processo e que execute um failover de teste antes de começar um failover completo para o site principal. [Saiba mais](site-recovery-failover.md) sobre o failover do Azure Site Recovery.
