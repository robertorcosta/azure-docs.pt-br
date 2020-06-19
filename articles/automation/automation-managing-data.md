---
title: Gerenciamento de dados da Automação do Azure
description: Este artigo fornece conceitos de gerenciamento de dados na Automação do Azure, incluindo retenção de dados e backup.
services: automation
ms.subservice: shared-capabilities
ms.date: 03/23/2020
ms.topic: conceptual
ms.openlocfilehash: de60ef31a39a698f9a797a5836546f9b75b67594
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83835199"
---
# <a name="management-of-azure-automation-data"></a>Gerenciamento de dados da Automação do Azure

Este artigo contém vários tópicos sobre o gerenciamento de dados em um ambiente da Automação do Azure.

## <a name="data-retention"></a>Retenção de dados

Quando você exclui um recurso na Automação do Azure, ele é mantido por vários dias para fins de auditoria antes da remoção permanente. Você não pode ver nem usar o recurso durante esse período. Essa política também se aplica aos recursos que pertencem a uma conta da Automação excluída.

A tabela a seguir resume a política de retenção para diferentes recursos.

| Dados | Política |
|:--- |:--- |
| Contas |Uma conta é removida permanentemente 30 dias depois que um usuário a exclui. |
| Ativos |Um ativo é removido permanentemente 30 dias depois que é excluído por um usuário, ou 30 dias depois que a conta que o contém é excluída por um usuário. |
| Nós DSC |Um nó DSC é removido permanentemente 30 dias após o cancelamento do registro de uma conta da Automação usando o portal do Azure ou o cmdlet [Unregister-AzAutomationDscNode](https://docs.microsoft.com/powershell/module/az.automation/unregister-azautomationdscnode?view=azps-3.7.0) no Windows PowerShell. Um nó também é removido permanentemente 30 dias após um usuário excluir a conta que o contém. |
| Trabalhos |Um trabalho é excluído e removido permanentemente 30 dias após a modificação, por exemplo, depois que o trabalho é concluído, interrompido ou suspenso. |
| Módulos |Um módulo é removido permanentemente 30 dias depois que é excluído por um usuário, ou 30 dias depois que a conta que o contém é excluída por um usuário. |
| Arquivos de configurações/MOF de nó |Uma configuração de nó antigo é removida permanentemente 30 dias depois que uma nova configuração de nó é gerada. |
| Relatórios de Nó |Um relatório de nó é removido de forma permanente 90 dias depois que um novo relatório é gerado para esse nó. |
| Runbooks |Um runbook é removido permanentemente 30 dias depois que é excluído por um usuário, ou 30 dias depois que a conta que o contém é excluída por um usuário. |

A política de retenção se aplica a todos os usuários e atualmente não pode ser personalizada. No entanto, se você precisar manter os dados por um longo período, [poderá encaminhar os dados do trabalho de Automação do Azure para os logs do Azure Monitor](automation-manage-send-joblogs-log-analytics.md).

## <a name="data-backup"></a>Backup de dados

Quando você exclui uma conta da Automação no Azure, todos os objetos na conta são excluídos. Os objetos incluem runbooks, módulos, configurações, definições, trabalhos e ativos. Eles não podem ser recuperados depois que a conta é excluída. Você pode usar as informações a seguir para fazer backup do conteúdo de sua conta da Automação antes de excluí-la.

### <a name="runbooks"></a>Runbooks

Você pode exportar seus runbooks para arquivos de script usando o Portal do Azure ou o cmdlet [Get-AzureAutomationRunbookDefinition](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationrunbookdefinition) no Windows PowerShell. Você pode importar esses arquivos de script para outra conta da Automação, conforme discutido em [Gerenciar runbooks na Automação do Azure](manage-runbooks.md).

### <a name="integration-modules"></a>Módulos de integração

Você não pode exportar módulos de integração da Automação do Azure. Você deve torná-los disponíveis fora da conta da Automação.

### <a name="assets"></a>Ativos

Você não pode exportar ativos da Automação do Azure: certificados, conexões, credenciais, agendamentos e variáveis. Em vez disso, você pode usar o portal do Azure e os cmdlets do Azure para observar os detalhes desses ativos. Em seguida, use esses detalhes para criar quaisquer ativos usados pelos runbooks importados para outra conta da Automação.

Não é possível recuperar os valores de variáveis criptografadas, nem os campos de senha de credenciais usando cmdlets. Se você não souber esses valores, poderá recuperá-los em um runbook. Para recuperar valores de variáveis, confira [Recursos variáveis na Automação do Azure](shared-resources/variables.md). Para saber mais sobre como recuperar valores de credenciais, confira [Recursos de credenciais na Automação do Azure](shared-resources/credentials.md).

 ### <a name="dsc-configurations"></a>Configurações DSC

Você pode exportar suas configurações DSC para arquivos de script usando o portal do Azure ou o cmdlet [Export-AzAutomationDscConfiguration](https://docs.microsoft.com/powershell/module/az.automation/export-azautomationdscconfiguration?view=azps-3.7.0
) no Windows PowerShell. Você pode importar e usar essas configurações em outra conta da Automação.

## <a name="geo-replication-in-azure-automation"></a>Replicação geográfica na Automação do Azure

A replicação geográfica é padrão nas contas da Automação do Azure. Você escolhe uma região primária ao configurar sua conta. O serviço de replicação geográfica interna da Automação atribui automaticamente uma região secundária à conta. Em seguida, o serviço faz o backup contínuo dos dados da conta da região primária para a região secundária. A lista completa de regiões primária e secundária pode ser encontrada em [Continuidade dos negócios e recuperação de desastres (BCDR): Regiões Combinadas do Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

O backup criado pelo serviço de replicação geográfica de automação é uma cópia completa dos ativos, configurações e similares da automação. Esse backup pode ser usado se a região principal falhar e perder dados. No evento improvável de os dados de uma região primária serem perdidos, a Microsoft tentará recuperá-los. Se a empresa não conseguir recuperar os dados principais, ela usa failover automático e informa a situação por meio da sua assinatura do Azure. 

O serviço de replicação geográfica da Automação não pode ser acessado diretamente por clientes externos se houver uma falha regional. Se você deseja manter a configuração da Automação e os runbooks durante falhas regionais:

1. Selecione uma região secundária a ser emparelhada com a região geográfica da sua conta da Automação primária.

2. Crie uma conta da Automação na região secundária.

3. Na conta principal, exporte seus runbooks como arquivos de script.

4. Importe os runbooks para sua conta da Automação na região secundária.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre ativos seguros na Automação do Azure, confira [Criptografia de ativos seguros na Automação do Azure](automation-secure-asset-encryption.md).
* Para saber mais sobre a replicação geográfica, confira [Criar e usar a replicação geográfica ativa](https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication).