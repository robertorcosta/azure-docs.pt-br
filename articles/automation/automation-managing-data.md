---
title: Gerenciando dados da Automação do Azure
description: Este artigo contém vários tópicos sobre o gerenciamento de um ambiente da Automação do Azure.  Atualmente, inclui a Retenção de dados e o backup da Recuperação de desastres na Automação do Azure.
services: automation
ms.subservice: shared-capabilities
ms.date: 03/23/2020
ms.topic: conceptual
ms.openlocfilehash: f917e9c64a932d75fd0f6b14c9e0f35808467355
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80984650"
---
# <a name="managing-azure-automation-data"></a>Gerenciando dados da Automação do Azure

Este artigo contém vários tópicos para gerenciar dados em um ambiente de automação do Azure.

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no seu Hybrid Runbook Worker, consulte [instalar o módulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de automação, você pode atualizar seus módulos para a versão mais recente usando [como atualizar os módulos de Azure PowerShell na automação do Azure](automation-update-azure-modules.md).

## <a name="data-retention"></a>Retenção de dados

Quando você exclui um recurso na automação do Azure, ele é retido por vários dias para fins de auditoria antes da remoção permanente. Você não pode ver ou usar o recurso durante esse tempo. Essa política também se aplica a recursos que pertencem a uma conta de automação excluída.

A tabela a seguir resume a política de retenção para diferentes recursos.

| Dados | Política |
|:--- |:--- |
| Contas |Uma conta é permanentemente removida 30 dias após o usuário excluí-la. |
| Ativos |Um ativo é removido permanentemente 30 dias depois que um usuário o exclui, ou 30 dias depois que um usuário exclui uma conta que contém o ativo. |
| Nós DSC |Um nó DSC é removido permanentemente 30 dias após ter cancelado o registro de uma conta de automação usando portal do Azure ou o cmdlet [Unregister-AzAutomationDscNode](https://docs.microsoft.com/powershell/module/az.automation/unregister-azautomationdscnode?view=azps-3.7.0) no Windows PowerShell. Um nó também é removido permanentemente 30 dias depois que um usuário exclui a conta que contém o nó. |
| Trabalhos |Um trabalho é excluído e removido permanentemente 30 dias após a modificação, por exemplo, após a conclusão do trabalho, é interrompido ou suspenso. |
| Módulos |Um módulo é removido permanentemente 30 dias depois que um usuário o exclui, ou 30 dias depois que um usuário exclui a conta que contém o módulo. |
| Arquivos de configurações/MOF de nó |Uma configuração de nó antiga é permanentemente removida 30 dias após a geração de uma nova configuração de nó. |
| Relatórios de Nó |Um relatório de nó é removido permanentemente 90 dias após a geração de um novo relatório para esse nó. |
| Runbooks |Um runbook é removido permanentemente 30 dias depois que um usuário exclui o recurso ou 30 dias depois que um usuário exclui a conta que contém o recurso. |

A política de retenção se aplica a todos os usuários e, no momento, não pode ser personalizada. No entanto, se você precisar manter os dados por um período mais longo, poderá [encaminhar os dados do trabalho de automação do Azure para Azure monitor logs](automation-manage-send-joblogs-log-analytics.md).

## <a name="data-backup"></a>Backup de dados

Quando você exclui uma conta de automação no Azure, todos os objetos na conta são excluídos. Os objetos incluem runbooks, módulos, configurações, configurações, trabalhos e ativos. Eles não podem ser recuperados depois que a conta é excluída. Você pode usar as informações a seguir para fazer backup do conteúdo da sua conta de automação antes de excluí-la.

### <a name="runbooks"></a>Runbooks

Você pode exportar seus runbooks para arquivos de script usando o Portal do Azure ou o cmdlet [Get-AzureAutomationRunbookDefinition](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationrunbookdefinition) no Windows PowerShell. Você pode importar esses arquivos de script para outra conta de automação, conforme discutido em [gerenciar runbooks na automação do Azure](manage-runbooks.md).

### <a name="integration-modules"></a>Módulos de integração

Não é possível exportar módulos de integração da automação do Azure. Você deve torná-los disponíveis fora da conta de automação.

### <a name="assets"></a>Ativos

Você não pode exportar ativos de automação do Azure: certificados, conexões, credenciais, agendas e variáveis. Em vez disso, você pode usar os cmdlets portal do Azure e do Azure para observar os detalhes desses ativos. Em seguida, use esses detalhes para criar todos os ativos que são usados por runbooks que você importa para outra conta de automação.

Você não pode recuperar os valores de variáveis criptografadas ou campos de senha de credenciais usando cmdlets. Se você não souber esses valores, poderá recuperá-los em um runbook. Para recuperar valores de variáveis, consulte [ativos de variáveis na automação do Azure](shared-resources/variables.md). Para saber mais sobre como recuperar valores de credencial, confira [ativos de credencial na automação do Azure](shared-resources/credentials.md).

 ### <a name="dsc-configurations"></a>Configurações DSC

Você pode exportar suas configurações DSC para arquivos de script usando o portal do Azure ou o cmdlet [Export-AzAutomationDscConfiguration](https://docs.microsoft.com/powershell/module/az.automation/export-azautomationdscconfiguration?view=azps-3.7.0
) no Windows PowerShell. Você pode importar e usar essas configurações em outra conta de automação.

## <a name="geo-replication-in-azure-automation"></a>Replicação geográfica na Automação do Azure

A replicação geográfica é padrão nas contas de automação do Azure. Você escolhe uma região primária ao configurar sua conta. O serviço de replicação geográfica de automação interna atribui uma região secundária à conta automaticamente. Em seguida, o serviço faz o backup contínuo dos dados da conta da região primária para a região secundária. A lista completa de regiões primárias e secundárias pode ser encontrada em [continuidade de negócios e recuperação de desastres (BCDR): regiões emparelhadas do Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

O backup criado pelo serviço de replicação geográfica de automação é uma cópia completa de ativos de automação, configurações e similares. Esse backup pode ser usado se a região primária ficar inativa e perder dados. No caso improvável de os dados de uma região primária serem perdidos, a Microsoft tentará recuperá-los. Se a empresa não puder recuperar os dados primários, ele usará o failover automático e informará você sobre a situação por meio de sua assinatura do Azure. 

O serviço de replicação geográfica de automação não estará acessível diretamente a clientes externos se houver uma falha regional. Se você quiser manter a configuração de automação e os runbooks durante falhas regionais:

1. Selecione uma região secundária a ser emparelhada com a região geográfica da sua conta de automação primária.

2. Crie uma conta de automação na região secundária.

3. Na conta principal, exporte seus runbooks como arquivos de script.

4. Importe os runbooks para sua conta de automação na região secundária.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre os ativos seguros na automação do Azure, confira [criptografar ativos seguros na automação do Azure](automation-secure-asset-encryption.md).

* Para saber mais sobre a replicação geográfica, consulte [criando e usando a replicação geográfica ativa](https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication).