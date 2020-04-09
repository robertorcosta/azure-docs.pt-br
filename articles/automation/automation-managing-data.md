---
title: Gerenciando dados da Automação do Azure
description: Este artigo contém vários tópicos sobre o gerenciamento de um ambiente da Automação do Azure.  Atualmente, inclui a Retenção de dados e o backup da Recuperação de desastres na Automação do Azure.
services: automation
ms.subservice: shared-capabilities
ms.date: 03/23/2020
ms.topic: conceptual
ms.openlocfilehash: f917e9c64a932d75fd0f6b14c9e0f35808467355
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984650"
---
# <a name="managing-azure-automation-data"></a>Gerenciando dados da Automação do Azure

Este artigo contém vários tópicos para o gerenciamento de dados em um ambiente de Automação Azure.

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no trabalhador do runbook híbrido, consulte [Instalar o Módulo PowerShell do Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de Automação, você pode atualizar seus módulos para a versão mais recente usando [Como atualizar módulos Azure PowerShell no Azure Automation](automation-update-azure-modules.md).

## <a name="data-retention"></a>Retenção de dados

Quando você exclui um recurso no Azure Automation, ele é retido por vários dias para fins de auditoria antes da remoção permanente. Você não pode ver ou usar o recurso durante este tempo. Essa política também se aplica a recursos que pertencem a uma conta de Automação excluída.

A tabela a seguir resume a política de retenção para diferentes recursos.

| Dados | Política |
|:--- |:--- |
| Contas |Uma conta é removida permanentemente 30 dias após o usuário excluí-la. |
| Ativos |Um ativo é removido permanentemente 30 dias após o usuário excluí-lo, ou 30 dias após um usuário excluir uma conta que detém o ativo. |
| Nós DSC |Um nó DSC é removido permanentemente 30 dias após não ser registrado em uma conta de Automação usando o portal Azure ou o cmdlet [Unregister-AzAutomationDscNode](https://docs.microsoft.com/powershell/module/az.automation/unregister-azautomationdscnode?view=azps-3.7.0) no Windows PowerShell. Um nó também é removido permanentemente 30 dias após um usuário excluir a conta que detém o nó. |
| Trabalhos |Um trabalho é suprimido e permanentemente removido 30 dias após a modificação, por exemplo, depois que o trabalho é concluído, é interrompido ou é suspenso. |
| Módulos |Um módulo é removido permanentemente 30 dias após o usuário excluí-lo, ou 30 dias após um usuário excluir a conta que mantém o módulo. |
| Arquivos de configurações/MOF de nó |Uma configuração de nó antigo é removida permanentemente 30 dias após a configuração de um novo nó ser gerada. |
| Relatórios de Nó |Um relatório de nó é permanentemente removido 90 dias após a publicação de um novo relatório para esse nó. |
| Runbooks |Um runbook é removido permanentemente 30 dias após o usuário excluir o recurso, ou 30 dias após um usuário excluir a conta que detém o recurso. |

A política de retenção se aplica a todos os usuários e atualmente não pode ser personalizada. No entanto, se você precisar manter os dados por um período mais longo, você pode [encaminhar os dados de trabalho do Azure Automation para logs do Azure Monitor](automation-manage-send-joblogs-log-analytics.md).

## <a name="data-backup"></a>Backup de dados

Quando você exclui uma conta de Automação no Azure, todos os objetos da conta são excluídos. Os objetos incluem runbooks, módulos, configurações, configurações, empregos e ativos. Eles não podem ser recuperados depois que a conta é excluída. Você pode usar as seguintes informações para fazer backup do conteúdo de sua conta de Automação antes de excluí-la.

### <a name="runbooks"></a>Runbooks

Você pode exportar seus runbooks para arquivos de script usando o Portal do Azure ou o cmdlet [Get-AzureAutomationRunbookDefinition](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationrunbookdefinition) no Windows PowerShell. Você pode importar esses arquivos de script para outra conta de Automação, conforme discutido em [Gerenciar runbooks no Azure Automation](manage-runbooks.md).

### <a name="integration-modules"></a>Módulos de integração

Você não pode exportar módulos de integração da Azure Automation. Você deve disponibilizá-los fora da conta de Automação.

### <a name="assets"></a>Ativos

Você não pode exportar ativos do Azure Automation: certificados, conexões, credenciais, horários e variáveis. Em vez disso, você pode usar o portal Azure e os cmdlets do Azure para observar os detalhes desses ativos. Em seguida, use esses detalhes para criar quaisquer ativos que são usados por runbooks que você importa para outra conta de Automação.

Você não pode recuperar os valores para variáveis criptografadas ou os campos de senha das credenciais usando cmdlets. Se você não conhece esses valores, você pode recuperá-los em um runbook. Para recuperar valores variáveis, consulte [Ativos variáveis no Azure Automation](shared-resources/variables.md). Para saber mais sobre como recuperar valores de credenciais, consulte [os ativos credenciais na Azure Automation](shared-resources/credentials.md).

 ### <a name="dsc-configurations"></a>Configurações DSC

Você pode exportar suas configurações de DSC para arquivos de script usando o portal Azure ou o [cmdlet Export-AzAutomationDscConfiguration](https://docs.microsoft.com/powershell/module/az.automation/export-azautomationdscconfiguration?view=azps-3.7.0
) no Windows PowerShell. Você pode importar e usar essas configurações em outra conta de Automação.

## <a name="geo-replication-in-azure-automation"></a>Replicação geográfica na Automação do Azure

A replicação geográfica é padrão nas contas de automação do Azure. Você escolhe uma região primária ao configurar sua conta. O serviço interno de georeplicação de automação atribui automaticamente uma região secundária à conta. O serviço, então, rescoria continuamente os dados da conta da região primária para a região secundária. A lista completa das regiões primárias e secundárias pode ser encontrada na [continuidade de negócios e recuperação de desastres (BCDR): Regiões emparelhadas do Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

O backup criado pelo serviço de georeplicação Automação é uma cópia completa dos ativos de Automação, configurações e afins. Esse backup pode ser usado se a região primária cair e perder dados. No caso improvável de que os dados de uma região primária sejam perdidos, a Microsoft tenta recuperá-los. Se a empresa não conseguir recuperar os dados primários, ele usa failover automático e informa a situação através de sua assinatura do Azure. 

O serviço de georeplicação de automação não é acessível diretamente a clientes externos se houver uma falha regional. Se você quiser manter a configuração de automação e os runbooks durante falhas regionais:

1. Selecione uma região secundária para emparelhar com a região geográfica da sua conta de Automação primária.

2. Crie uma conta de Automação na região secundária.

3. Na conta principal, exporte seus runbooks como arquivos de script.

4. Importe os runbooks para sua conta de Automação na região secundária.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre ativos seguros no Azure Automation, consulte [Criptografar ativos seguros no Azure Automation](automation-secure-asset-encryption.md).

* Para saber mais sobre a geo-replicação, consulte [Criando e usando a geo-replicação ativa](https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication).