---
title: Suporte ao módulo Az na Automação do Azure
description: Este artigo fornece informações sobre o uso de módulos Az na Automação Azure.
services: automation
ms.subservice: shared-capabilities
ms.date: 02/08/2019
ms.topic: conceptual
ms.openlocfilehash: 776834937d81a3ba84e3c1db56496a7d951d7982
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548352"
---
# <a name="az-module-support-in-azure-automation"></a>Suporte ao módulo Az na Automação do Azure

A Azure Automation suporta o uso do [módulo Azure PowerShell Az](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) em seus runbooks. O módulo Az não é importado automaticamente em quaisquer contas de Automação novas ou existentes. 

## <a name="considerations"></a>Considerações

Há muitos pontos a considerar ao usar o módulo Az na Automação do Azure. Os runbooks e módulos podem ser usados por soluções de nível superior em sua conta de Automação. A edição de runbooks ou a atualização de módulos podem potencialmente causar problemas com os runbooks. Você deve testar todos os runbooks e soluções cuidadosamente `Az` em uma conta de automação separada antes de importar os novos módulos. Quaisquer modificações nos módulos podem afetar negativamente a solução [Start/Stop.](automation-solution-vm-management.md) Não recomendamos alterar módulos e runbooks em contas de automação que contenham quaisquer soluções. Esse comportamento não é específico dos módulos Az. Esse comportamento deve ser levado em consideração ao introduzir quaisquer alterações em sua conta de Automação.

Importar um `Az` módulo em sua conta de automação não importa automaticamente o módulo na sessão PowerShell que os runbooks usam. Os módulos são importados na sessão do PowerShell nas seguintes situações:

* Quando um cmdlet de um módulo é chamado de um runbook
* Quando um runbook o importa explicitamente com o cmdlet `Import-Module`
* Quando outro módulo dependente do módulo é importado em uma sessão do PowerShell

> [!IMPORTANT]
> É importante garantir que os runbooks em uma `Az` `AzureRM` conta de Automação apenas importem ou módulos nas sessões do PowerShell usadas por runbooks e não em ambos. Se `Az` for importado `AzureRM` antes em um runbook, o livro de execução é concluído, mas um erro referente ao [cmdlet Get_SerializationSettings](troubleshoot/runbooks.md#get-serializationsettings) aparece nos fluxos de trabalho e os cmdlets podem não ser executados corretamente. Se você `AzureRM` importar `Az`e, em seguida, seu runbook ainda é concluído, `Az` `AzureRM` mas você recebe um erro nos fluxos de trabalho afirmando que ambos e não podem ser importados na mesma sessão ou usados no mesmo runbook.

## <a name="migrating-to-az-modules"></a>Migrar para módulos Az

Recomenda-se que você teste a migração para módulos Az em uma conta de automação de teste. Uma vez que a conta de Automação tenha sido criada, você pode usar as instruções nesta seção para trabalhar com os módulos.

### <a name="stop-and-unschedule-all-runbooks-that-use-azurerm-cmdlets"></a>Pare e desmarque todos os runbooks que usam cmdlets AzureRM

Para garantir que você não execute os runbooks existentes que usam os cmdlets `AzureRM`, é preciso parar e cancelar o agendamento de todos os runbooks que usam os módulos `AzureRM`. Você pode ver quais horários existem e quais horários devem ser removidos executando código semelhante a este exemplo.

  ```powershell-interactive
  Get-AzureRmAutomationSchedule -AutomationAccountName "<AutomationAccountName>" -ResourceGroupName "<ResourceGroupName>" | Remove-AzureRmAutomationSchedule -WhatIf
  ```

É importante revisar cada cronograma separadamente para garantir que você possa reagendar no futuro para seus runbooks, se necessário.

### <a name="import-the-az-modules"></a>Importar os módulos Az

Importe somente os módulos Az que são necessários para seus runbooks. Não importe o `Az` módulo de rollup, `Az.*` pois inclui todos os módulos. Essa orientação é a mesma para todos os módulos.

O módulo [Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) é uma dependência para os outros módulos `Az.*`. Por essa razão, este módulo precisa ser importado para sua conta de Automação antes de importar quaisquer outros módulos.

A partir de sua conta de automação, selecione **Módulos** em **Recursos Compartilhados**. Clique em **Procurar na Galeria** para abrir a página **Procurar na Galeria**.  Na barra de pesquisa, digite o nome do módulo (como `Az.Accounts`). Na página do Módulo PowerShell, clique **em Importar** para importar o módulo para sua conta de Automação.

![Importar módulos da conta de automação](media/az-modules/import-module.png)

Esse processo de importação também pode ser feito através da [Galeria PowerShell,](https://www.powershellgallery.com) procurando o módulo para importação. Depois de encontrar o módulo, selecione-o e, na guia **Automação do Azure**, clique em **Implantar na Automação do Azure**.

![Importar módulos diretamente da galeria](media/az-modules/import-gallery.png)

## <a name="testing-your-runbooks"></a>Testando seus runbooks

Uma `Az` vez que os módulos são importados para sua conta de Automação, você pode começar a editar seus runbooks para usar os módulos Az. A maioria dos cmdlets tem os `AzureRM` mesmos `Az`nomes, exceto que foi alterado para . Para obter uma lista de módulos que não seguem esta convenção de nomeação, consulte [a lista de exceções](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters).

Uma maneira de testar a modificação de um runbook para `Enable-AzureRMAlias -Scope Process` usar os novos cmdlets é usando no início do runbook. Adicionando este comando ao seu runbook, o script pode ser executado sem alterações.

## <a name="after-migration-details"></a>Detalhes pós-migração

Depois que a migração estiver concluída, não `AzureRM` tente iniciar os runbooks usando módulos na conta de Automação por mais tempo. Também é recomendado não importar ou `AzureRM` atualizar módulos na conta. Considere a conta `Az`migrada para `Az` , e opere apenas com módulos. 

Quando uma nova conta de Automação `AzureRM` é criada, os módulos existentes ainda são instalados. Você ainda pode atualizar os `AzureRM` runbooks tutoriais com cmdlets. Você não deve executar esses livros de execução.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como usar módulos Az, consulte [Introdução ao módulo Az](/powershell/azure/get-started-azureps?view=azps-1.1.0).
