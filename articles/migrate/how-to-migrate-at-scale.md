---
title: Automatize a migração de máquinas de migração no Azure Migrate
description: Descreve como usar scripts para migrar um grande número de máquinas no Azure Migrate
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: article
ms.date: 04/01/2019
ms.author: snehaa
ms.openlocfilehash: 317b6e8aa799b7982e9897c6a504d6092491c7ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74196355"
---
# <a name="scale-migration-of-vms"></a>Migração em escala de VMs 

Este artigo ajuda você a entender como usar scripts para migrar um grande número de máquinas virtuais (VMs). Para dimensionar a migração, você usa [o Azure Site Recovery](../site-recovery/site-recovery-overview.md). 

Os scripts de recuperação do site estão disponíveis para download no repo [Azure PowerShell Samples](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-with-site-recovery) no GitHub. Os scripts podem ser usados para migrar VMware, AWS, VMs GCP e servidores físicos para discos gerenciados no Azure. Também é possível usar esses scripts para migrar VMs Hyper-V, se você migrar as VMs como servidores físicos. Os scripts que aproveitam o Azure Site Recovery PowerShell são documentados [aqui](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell).

## <a name="current-limitations"></a>Limitações atuais
- Compatível com especificação do endereço IP estático somente para o NIC principal da VM de destino
- Os scripts não terão o Benefício Híbrido do Azure relacionados às entradas, você precisará atualizar manualmente as propriedades da VM replicada no portal

## <a name="how-does-it-work"></a>Como ele funciona?

### <a name="prerequisites"></a>Pré-requisitos
Antes de começar, você precisa realizar as seguintes etapas:
- Certifique-se de que o cofre do Site Recovery foi criado na sua assinatura do Azure
- Certifique-se de que o Servidor de Configuração e o Servidor de Processo foram instalados no ambiente de origem e o cofre é capaz de descobrir o ambiente
- Certifique-se de que uma Política de Replicação foi criada e associada com o Servidor de Configuração
- Certifique-se de que você adicionou a conta de administração VM ao servidor de configuração (que será usado para replicar as VMs no local)
- Certifique-se de que os artefatos de destino no Azure sejam criados
    - Grupo de recursos de destino
    - Conta de armazenamento alvo (e seu grupo de recursos) - Crie uma conta de armazenamento premium se você planeja migrar para discos gerenciados premium
    - Conta de armazenamento de cache (e seu grupo de recursos) - Crie uma conta de armazenamento padrão na mesma região que o cofre
    - Rede virtual de destino para failover (e seu grupo de recursos)
    - Sub-rede de destino
    - Rede virtual de destino para failover de teste (e seu grupo de recursos)
    - Conjunto de disponibilidade (se necessário)
    - Grupo de segurança de rede de destino e seu grupo de recursos
- Certifique-se de ter decidido sobre as propriedades da VM de destino
    - Nome da VM de destino
    - Tamanho da VM de destino no Azure (pode ser decidido usando a avaliação de Migrações para Azure)
    - Endereço IP privado da NIC primária na VM
- Baixar os scripts do repositório [Exemplos do Azure PowerShell](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-with-site-recovery) no GitHub

### <a name="csv-input-file"></a>Arquivo de entrada CSV
Uma vez que você tenha todos os pré-requisitos concluídos, você precisa criar um arquivo CSV, que tem dados para cada máquina de origem que você deseja migrar. O CSV de entrada deve ter uma linha de cabeçalho com os detalhes de entrada e uma linha com os detalhes de cada máquina que precisa ser migrada. Todos os scripts são desenvolvidos para trabalhar no mesmo arquivo CSV. Um modelo de CSV de exemplo está disponível na pasta de scripts para sua referência.

### <a name="script-execution"></a>Execução do script
Quando o CSV estiver pronto, você poderá executar as etapas abaixo para realizar a migração das VMs no local:

**Etapa nº** | **Nome do script** | **Descrição**
--- | --- | ---
1 | asr_startmigration.ps1 | Habilitar a replicação para todas as VMs listadas no csv, o script cria uma saída CSV com os detalhes do trabalho para cada VM
2 | asr_replicationstatus.ps1 | Verificar o status da replicação, o script cria um csv com o status para cada VM
3 | asr_updateproperties.ps1 | Depois que as VMs são replicadas/protegidas, use este script para atualizar as propriedades de destino da VM (Propriedades de computação e rede)
4 | asr_propertiescheck.ps1 | Verificar se as propriedades são atualizadas adequadamente
5 | asr_testmigration.ps1 |  Iniciar o teste de failover das VMs listadas no csv, o script cria uma saída CSV com os detalhes do trabalho para cada VM
6 | asr_cleanuptestmigration.ps1 | Depois de validar manualmente as VMs que falharam o teste de failover, você pode usar esse script para limpar as VMs do teste de failover
7 | asr_migration.ps1 | Realizar um failover não planejado para as VMs listadas no csv, o script cria uma saída CSV com os detalhes do trabalho para cada VM. O script não desliga as VMs no local antes de acionar o failover, para consistência do aplicativo, recomenda-se que você desligue manualmente as VMs antes de executar o script.
8 | asr_completemigration.ps1 | Realize a operação de confirmação nas VMs e exclua as entidades de recuperação do site do Azure
9 | asr_postmigration.ps1 | Se você planeja atribuir grupos de segurança de rede aos NICs após o failover, você pode usar esse script para fazer isso. Ele atribui um NSG a qualquer NIC na VM alvo.

## <a name="how-to-migrate-to-managed-disks"></a>Como migrar para discos gerenciados?
O script, por padrão, migra as VMs para discos gerenciados no Azure. Se a conta de armazenamento de destino fornecida for uma conta de armazenamento premium, os discos gerenciados por premium serão criados após a migração. A conta de armazenamento de cache ainda pode ser uma conta padrão. Se a conta de armazenamento de destino for uma conta de armazenamento padrão, os discos padrão serão criados após a migração. 

## <a name="next-steps"></a>Próximas etapas

[Saiba mais](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure) sobre como migrar os servidores no Azure usando o Azure Site Recovery
