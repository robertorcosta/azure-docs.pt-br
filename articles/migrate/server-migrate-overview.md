---
title: Selecione uma opção de migração VMware com migração de servidor migrações para Azure | Microsoft Docs
description: Fornece uma visão geral das opções para migrar VMs VMware para o Azure com migração de servidor de migrações para Azure
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: raynew
ms.openlocfilehash: 68026af5b96728ea66fd9e584e67e5e596e690fb
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974658"
---
# <a name="select-a-vmware-migration-option"></a>Selecione uma opção de migração do VMware

É possível migrar VMs VMware para o Azure usando a ferramenta de Migração de Servidor de Migrações para Azure. Essa ferramenta oferece algumas opções para a migração de VM VMware:

- Migração usando a replicação sem agente. Migre VMs sem precisar instalar nada nelas.
- Migração com um agente para replicação. Instale um agente na VM para replicação.




## <a name="compare-migration-methods"></a>Comparar métodos de migração

Use essas comparações selecionadas para ajudá-lo a decidir qual método usar. Você também pode examinar os requisitos de suporte completo para migração [baseada em](migrate-support-matrix-vmware.md#agent-based-migration-vmware-server-requirements) agente e sem [agentes](migrate-support-matrix-vmware.md#agentless-migration-vmware-server-requirements) .

**Configuração** | **Sem agente** | **Baseado em agente**
--- | --- | ---
**Permissões do Azure** | Você precisa de permissões para criar um projeto de migrações para Azure e registrar os aplicativos do Azure AD criados ao implantar o dispositivo de migrações para Azure. | Você precisa de permissões de colaborador na assinatura do Azure. 
**Replicação simultânea** | Um máximo de 100 VMs pode ser replicado simultaneamente de um vCenter Server.<br/> Se você tiver mais de 50 VMs para migração, crie vários lotes de VMs.<br/> Replicar mais em uma única vez afetará o desempenho. | ND
**Implantação de dispositivo** | O [dispositivo migrações para Azure](migrate-appliance.md) é implantado localmente. | O [dispositivo de replicação de migrações para Azure](migrate-replication-appliance.md) é implantado localmente.
**Compatibilidade Site Recovery** | Compatíveis. | Você não poderá replicar com a migração de servidor de migrações para Azure se tiver configurado a replicação para um computador usando Site Recovery.
**Disco de destino** | Managed Disks | Managed Disks
**Limites de disco** | Disco do sistema operacional: 2 TB<br/><br/> Disco de dados: 4 TB<br/><br/> Máximo de discos: 60 | Disco do sistema operacional: 2 TB<br/><br/> Disco de dados: 8 TB<br/><br/> Máximo de discos: 63
**Discos de passagem** | Sem suporte | Com suporte
**Inicialização UEFI** | Sem suporte | A VM migrada no Azure será convertida automaticamente em uma VM de inicialização do BIOS.<br/><br/> O disco do sistema operacional deve ter até quatro partições, e os volumes devem ser formatados com NTFS.


## <a name="deployment-steps-comparison"></a>Comparação de etapas de implantação

Depois de revisar as limitações, entender as etapas envolvidas na implantação de cada solução pode ajudá-lo a decidir qual opção escolher.

**Tarefa** | **Detalhes** |**Sem agente** | **Baseado em agente**
--- | --- | --- | ---
**Avaliação** | Avalie servidores antes da migração.  A avaliação é opcional. Sugerimos que você avalie as máquinas antes de migrá-las, mas não é necessário. <br/><br/> Para avaliação, as migrações para Azure configuram um dispositivo leve para descobrir e avaliar as VMs. | Se você executar uma migração sem agente após a avaliação, o mesmo dispositivo de migração do Azure configurado para avaliação será usado para a migração sem agente.  |  Se você executar uma migração baseada em agente após a avaliação, a configuração do dispositivo para avaliação não será usada durante a migração sem agente. Você pode deixar o dispositivo em vigor ou removê-lo se não quiser fazer mais descoberta e avaliação.
**Preparar servidores VMware e VMs para migração** | Defina várias configurações em servidores VMware e VMs. | obrigatórios | obrigatórios
**Adicionar a ferramenta de migração de servidor** | Adicione a ferramenta de migração de servidor de migrações para Azure no projeto de migrações para Azure. | obrigatórios | obrigatórios
**Implantar o dispositivo de migrações para Azure** | Configure um dispositivo leve em uma VM VMware para avaliação e descoberta de VM. | obrigatórios | Não obrigatório.
**Instalar o serviço de mobilidade em VMs** | Instalar o serviço de mobilidade em cada VM que você deseja replicar | Não obrigatório | obrigatórios
**Implantar o dispositivo de replicação de migração de servidor de migrações para Azure** | Configurar um dispositivo em uma VM VMware para descobrir VMs e fazer a ponte entre o serviço de mobilidade em execução em VMs e migração de servidor de migrações para Azure | Não obrigatório | obrigatórios
**Replicar VMs**. Habilite a replicação da VM. | Definir as configurações de replicação e selecionar as VMs a serem replicadas | obrigatórios | obrigatórios
**Executar uma migração de teste** | Executar uma migração de teste para verificar se tudo está funcionando conforme o esperado. | obrigatórios | obrigatórios
**Executar uma migração completa** | Migre as VMs. | obrigatórios | obrigatórios




## <a name="next-steps"></a>Próximos passos

[Migre VMs VMware](tutorial-migrate-vmware.md) com migração sem agente.



