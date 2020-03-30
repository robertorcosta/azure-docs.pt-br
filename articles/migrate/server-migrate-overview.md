---
title: Selecione uma opção de migração do VMware com a migração do servidor Azure Migrate | Microsoft Docs
description: Fornece uma visão geral das opções para migrar VMs VMware para o Azure com migração de servidor migração do Azure Migrate
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: 52e7103ea3ebcd83369a866cc3f75b0bf0e889a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76028721"
---
# <a name="select-a-vmware-migration-option"></a>Selecione uma opção de migração vMware

É possível migrar VMs VMware para o Azure usando a ferramenta de Migração de Servidor de Migrações para Azure. Essa ferramenta oferece algumas opções para a migração de VM VMware:

- Migração usando a replicação sem agente. Migre VMs sem precisar instalar nada nelas.
- Migração com um agente para replicação. Instale um agente na VM para replicação.




## <a name="compare-migration-methods"></a>Compare os métodos de migração

Use essas comparações selecionadas para ajudá-lo a decidir qual método usar. Você também pode rever os requisitos completos de suporte para migração [sem agente](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) e [baseada em agentes.](migrate-support-matrix-vmware-migration.md#agent-based-vmware-servers)

**Configuração** | **Sem agente** | **Baseado em agentes**
--- | --- | ---
**Permissões do Azure** | Você precisa de permissões para criar um projeto do Azure Migrate e para registrar aplicativos Azure AD criados quando você implantar o aparelho Azure Migrate. | Você precisa de permissões do Contribuinte na assinatura do Azure. 
**Replicação simultânea** | Um máximo de 100 VMs pode ser replicado simultaneamente a partir de um servidor vCenter.<br/> Se você tiver mais de 50 VMs para migração, crie vários lotes de VMs.<br/> Replicar mais em um único momento afetará o desempenho. | NA
**Implantação do aparelho** | O [aparelho Azure Migrate](migrate-appliance.md) é implantado no local. | O [aparelho De replicação Azure Migrate](migrate-replication-appliance.md) é implantado no local.
**Compatibilidade de recuperação de site** | Compatível. | Você não pode replicar com a migração do servidor Azure Migrate se tiver configurado a replicação de uma máquina usando o Site Recovery.
**Disco de destino** | Discos gerenciados | Discos gerenciados
**Limites de disco** | Disco do SISTEMA OPERACIONAL: 2 TB<br/><br/> Disco de dados: 4 TB<br/><br/> Discos máximos: 60 | Disco do SISTEMA OPERACIONAL: 2 TB<br/><br/> Disco de dados: 8 TB<br/><br/> Discos máximos: 63
**Discos de passagem** | Sem suporte | Com suporte
**Inicialização UEFI** | Sem suporte | A VM migrada no Azure será automaticamente convertida em uma VM de inicialização do BIOS.<br/><br/> O disco do SISTEMA OPERACIONAL deve ter até quatro partições, e os volumes devem ser formatados com NTFS.


## <a name="deployment-steps-comparison"></a>Comparação de etapas de implantação

Depois de rever as limitações, entender as etapas envolvidas na implantação de cada solução pode ajudá-lo a decidir qual opção escolher.

**Tarefa** | **Detalhes** |**Sem agente** | **Baseado em agentes**
--- | --- | --- | ---
**Avaliação** | Avalie os servidores antes da migração.  A avaliação é opcional. Sugerimos que você avalie as máquinas antes de migrá-las, mas não precisa. <br/><br/> Para avaliação, o Azure Migrate configura um aparelho leve para descobrir e avaliar VMs. | Se você executar uma migração sem agente após a avaliação, o mesmo aparelho Azure Migrate configurado para avaliação é usado para migração sem agente.  |  Se você executar uma migração baseada em agente após a avaliação, o aparelho configurado para avaliação não será usado durante a migração sem agente. Você pode deixar o aparelho no lugar ou removê-lo se não quiser fazer mais descobertas e avaliações.
**Prepare servidores VMware e VMs para migração** | Configure uma série de configurações em servidores VMware e VMs. | Obrigatório | Obrigatório
**Adicione a ferramenta Migração do servidor** | Adicione a ferramenta Migração do Servidor Azure migrate no projeto Azure Migrate. | Obrigatório | Obrigatório
**Implantar o aparelho Azure Migrate** | Configure um aparelho leve em uma VMware VMware para detecção e avaliação de VM. | Obrigatório | Não obrigatório.
**Instale o serviço de Mobilidade em VMs** | Instale o serviço de mobilidade em cada VM que você deseja replicar | Não obrigatório | Obrigatório
**Implantar o dispositivo de replicação de migração do servidor Azure Migrate** | Configure um aparelho em uma VMware VMpara descobrir VMs e fazer uma ponte entre o serviço de mobilidade em execução em VMs e a migração do servidor Azure Migrate | Não obrigatório | Obrigatório
**Replicar VMs**. Habilitar a replicação de VM. | Configure as configurações de replicação e selecione VMs para replicar | Obrigatório | Obrigatório
**Execute um teste de migração** | Executar uma migração de teste para verificar se tudo está funcionando conforme o esperado. | Obrigatório | Obrigatório
**Execute uma migração completa** | Migre as VMs. | Obrigatório | Obrigatório




## <a name="next-steps"></a>Próximas etapas

[Migre as VMMs vMware](tutorial-migrate-vmware.md) com migração sem agente.



