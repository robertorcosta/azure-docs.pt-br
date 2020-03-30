---
title: Gerenciando dados da Automação do Azure
description: Este artigo contém vários tópicos sobre o gerenciamento de um ambiente da Automação do Azure.  Atualmente, inclui a Retenção de dados e o backup da Recuperação de desastres na Automação do Azure.
services: automation
ms.subservice: shared-capabilities
ms.date: 03/23/2020
ms.topic: conceptual
ms.openlocfilehash: 340fa19b6f9f07e192123900bc96b07bb016542f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132890"
---
# <a name="managing-azure-automation-data"></a>Gerenciando dados da Automação do Azure

Este artigo contém vários tópicos sobre o gerenciamento de um ambiente da Automação do Azure.

## <a name="data-retention"></a>Retenção de dados

Quando você exclui um recurso no Azure Automation, ele é retido por 30 dias para fins de auditoria antes de ser removido permanentemente. Você não pode ver ou usar o recurso durante este tempo. Essa política também se aplica a recursos que pertencem a uma conta de automação que é excluída.

O Azure Automation exclui e remove permanentemente trabalhos com mais de 30 dias.

A tabela a seguir resume a política de retenção para diferentes recursos.

| Dados | Política |
|:--- |:--- |
| Contas |Removido permanentemente 30 dias após a conta ser excluída por um usuário. |
| Ativos |Removido permanentemente 30 dias após o recurso ser excluído por um usuário, ou 30 dias após a conta que detém o ativo ser excluída por um usuário. |
| Módulos |Removido permanentemente 30 dias após o módulo ser excluído por um usuário, ou 30 dias após a conta que mantém o módulo ser excluída por um usuário. |
| Runbooks |Removido permanentemente 30 dias após o recurso ser excluído por um usuário, ou 30 dias após a conta que mantém o recurso ser excluída por um usuário. |
| Trabalhos |Excluído e removido permanentemente 30 dias após a última modificação. Isso pode ocorrer depois que o trabalho é concluído, interrompido ou suspenso. |
| Arquivos de configurações/MOF de nó |A configuração do nó antigo é removida permanentemente 30 dias após a configuração de um novo nó ser gerada. |
| Nós DSC |Removido permanentemente 30 dias após o nó não ser registrado na Conta de Automação usando o portal Azure ou o cmdlet [Unregister-AzureRMAutomationDscNode](https://docs.microsoft.com/powershell/module/azurerm.automation/unregister-azurermautomationdscnode) no Windows PowerShell. Os nós também são removidos permanentemente 30 dias após a conta que mantém o nó ser excluída por um usuário. |
| Relatórios de Nó |Removido de forma permanente 90 dias depois que um novo relatório é gerado para esse nó |

A política de retenção se aplica a todos os usuários e, atualmente não, pode ser personalizada.

No entanto, se você precisar reter dados por um período maior de tempo, você pode encaminhar registros de trabalho de runbook para logs do Monitor do Azure. Para obter mais informações, consulte [os dados de trabalho do Azure Automation para os registros do Azure Monitor](automation-manage-send-joblogs-log-analytics.md).

## <a name="backing-up-azure-automation"></a>Fazendo backup da Automação do Azure

Quando você exclui uma conta de automação no Microsoft Azure, todos os objetos na conta são excluídos, incluindo runbooks, módulos, configurações, trabalhos e ativos. Os objetos não podem ser recuperados depois que a conta é excluída.  Você pode usar as informações a seguir para fazer backup do conteúdo de sua conta de automação antes de excluí-la.

### <a name="runbooks"></a>Runbooks

Você pode exportar seus runbooks para arquivos de script usando o Portal do Azure ou o cmdlet [Get-AzureAutomationRunbookDefinition](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationrunbookdefinition) no Windows PowerShell. Esses arquivos de script podem ser importados para outra conta de automação, conforme discutido em [Criando ou importando um runbook](/previous-versions/azure/dn643637(v=azure.100)).

### <a name="integration-modules"></a>Módulos de integração

Você não pode exportar módulos de integração da Automação do Azure. Você deve garantir que eles estejam disponíveis fora da conta de automação.

### <a name="assets"></a>Ativos

Não é possível exportar [ativos](/previous-versions/azure/dn939988(v=azure.100)) da Automação do Azure.  Usando o Portal do Azure, você deve observar os detalhes de variáveis, credenciais, certificados, conexões e agendas.  Você deve criar manualmente qualquer ativo que seja usado por runbooks importados para outra automação.

Você pode usar [cmdlets do Azure](https://docs.microsoft.com/powershell/module/azurerm.automation#automation) para recuperar os detalhes de ativos não criptografados e salvá-los para referência futura ou criar ativos equivalentes em outra conta de automação.

Não é possível recuperar o valor de variáveis criptografadas nem o campo de senha de credenciais usando cmdlets. Se você não souber esses valores, poderá recuperá-los em um runbook usando as atividades [Get-AutomationVariable](/previous-versions/azure/dn940012(v=azure.100)) e [Get-AutomationPSCredential](/previous-versions/azure/dn940015(v=azure.100)).

Você não pode exportar certificados da Automação do Azure. Você deve garantir que todos os certificados estejam disponíveis fora do Azure.

### <a name="dsc-configurations"></a>Configurações DSC

Você pode exportar suas configurações para arquivos de script usando o Portal do Azure ou o cmdlet [Export-AzureRmAutomationDscConfiguration](https://docs.microsoft.com/powershell/module/azurerm.automation/export-azurermautomationdscconfiguration) no Windows PowerShell. Essas configurações podem ser importadas e usadas em outra conta de automação.

## <a name="geo-replication-in-azure-automation"></a>Replicação geográfica na Automação do Azure

A replicação geográfica, padrão em contas da Automação do Azure, faz o backup de dados da conta em uma região geográfica diferente para obter redundância. Você pode escolher uma região primária durante a configuração de sua conta e, em seguida, uma região secundária é atribuída automaticamente a ela. Os dados secundários, copiados da região primária, são atualizados continuamente em caso de perda de dados.  

A tabela a seguir mostra os emparelhamentos disponíveis das regiões primárias e secundárias.

| Primária | Secundário |
| --- | --- |
| Centro-Sul dos Estados Unidos |Centro-Norte dos EUA |
| Leste dos EUA 2 |Centro dos EUA |
| Europa Ocidental |Norte da Europa |
| Sudeste da Ásia |Leste da Ásia |
| Leste do Japão |Oeste do Japão |

No evento improvável de os dados de uma região primária serem perdidos, a Microsoft tentará recuperá-los. Se não for possível recuperar os dados primários, o failover geográfico será executado e os clientes afetados receberão uma notificação em suas assinaturas.
