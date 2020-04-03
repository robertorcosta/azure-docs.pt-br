---
title: Suporte ao módulo Az na Automação do Azure
description: Este artigo fornece informações sobre o uso de módulos Az na Automação Azure.
services: automation
ms.subservice: shared-capabilities
ms.date: 02/08/2019
ms.topic: conceptual
ms.openlocfilehash: 21fa1c4faa4a080b9b495e1481fdadcd7e8bea10
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619470"
---
# <a name="az-module-support-in-azure-automation"></a>Suporte ao módulo Az na Automação do Azure

A Azure Automation suporta o uso do [módulo Azure PowerShell Az](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) em seus runbooks. O módulo Rollup Az não é importado automaticamente em quaisquer contas de Automação novas ou existentes. 

## <a name="considerations"></a>Considerações

Há muitas coisas a serem consideradas ao usar o módulo Rollup Az na Azure Automation. Os runbooks e módulos podem ser usados por soluções de nível superior em sua conta de Automação. A edição de runbooks ou a atualização de módulos podem potencialmente causar problemas com os runbooks. Você deve testar todos os runbooks e soluções cuidadosamente em uma conta de automação separada antes de importar novos módulos Az. Quaisquer modificações nos módulos podem afetar negativamente a solução [Start/Stop.](automation-solution-vm-management.md) Não recomendamos alterar módulos e runbooks em contas de automação que contenham quaisquer soluções. Esse comportamento não é específico dos módulos Az. Deve ser levado em consideração ao introduzir quaisquer alterações em sua conta de Automação.

Importar um módulo Az em sua conta de automação não importa automaticamente o módulo na sessão PowerShell que os runbooks usam. Os módulos são importados na sessão do PowerShell nas seguintes situações:

* Quando um runbook invoca um cmdlet de um módulo
* Quando um runbook importa o `Import-Module` módulo explicitamente com o cmdlet
* Quando um runbook importa outro módulo dependendo do módulo

> [!IMPORTANT]
> Certifique-se de que os runbooks em uma conta de automação importem módulos Az ou módulos [AzureRM,](https://www.powershellgallery.com/packages/AzureRM/6.13.1) mas não ambos, em uma sessão PowerShell. Se um runbook importa módulos Az antes dos módulos AzureRM, o runbook será concluído. No entanto, um erro referente ao [Get_SerializationSettings](troubleshoot/runbooks.md#get-serializationsettings) cmdlet aparece nos fluxos de trabalho e os cmdlets podem não ser executados corretamente. Se o runbook importa módulos AzureRM antes dos módulos Az, o runbook também será concluído. Neste caso, porém, você recebe um erro nos fluxos de trabalho afirmando que tanto Az quanto AzureRM não podem ser importados na mesma sessão ou usados no mesmo runbook.

## <a name="migrating-to-az-modules"></a>Migrar para módulos Az

Recomenda-se que você teste uma migração para módulos Az em uma conta de automação de teste. Depois de criar essa conta, você pode usar as instruções nesta seção para trabalhar com os módulos.

### <a name="stop-and-unschedule-all-runbooks-that-use-azurerm-modules"></a>Pare e desmarque todos os runbooks que usam módulos AzureRM

Para garantir que você não execute nenhum runbooks existente que use módulos AzureRM, pare e desprograme todos os runbooks afetados. Você pode ver quais horários existem e quais horários remover executando código semelhante a este exemplo:

  ```powershell-interactive
  Get-AzureRmAutomationSchedule -AutomationAccountName "<AutomationAccountName>" -ResourceGroupName "<ResourceGroupName>" | Remove-AzureRmAutomationSchedule -WhatIf
  ```

É importante revisar cada cronograma separadamente para garantir que você possa reagendar no futuro para seus runbooks, se necessário.

### <a name="import-the-az-modules"></a>Importar os módulos Az

>[!NOTE]
>Que seus runbooks importem apenas módulos Az necessários. Não importe o módulo Az rollup, pois inclui todos os módulos Az. Essa orientação é a mesma para todos os módulos.

O [módulo Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) é uma dependência para os outros módulos Az. Por essa razão, seus runbooks devem importar este módulo para sua conta de Automação antes de importar quaisquer outros módulos.

Para importar os módulos no portal Azure:

1. A partir de sua conta de automação, selecione **Módulos** em **Recursos Compartilhados**. 
2. Clique em **Procurar na Galeria** para abrir a página Procurar na Galeria.  
3. Na barra de pesquisa, digite o `Az.Accounts`nome do módulo, por exemplo, . 
4. Na página do Módulo PowerShell, clique **em Importar** para importar o módulo para sua conta de Automação.

![Importar módulos da conta de automação](media/az-modules/import-module.png)

Esse processo de importação também pode ser feito através da [Galeria PowerShell,](https://www.powershellgallery.com) procurando o módulo para importação. Depois de encontrar o módulo, selecione-o, escolha a guia **Automação Do Azul** e clique em Implantar no **Azure Automation**.

![Importar módulos diretamente da galeria](media/az-modules/import-gallery.png)

## <a name="testing-your-runbooks"></a>Testando seus runbooks

Depois de importar os módulos Az para a conta Automação, você pode começar a editar seus runbooks para usar os módulos. A maioria dos cmdlets tem os mesmos nomes do módulo AzureRM, exceto que o prefixo AzureRM (ou AzureRm) foi alterado para Az. Para obter uma lista de módulos que não seguem esta convenção de nomeação, consulte [a lista de exceções](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters).

Uma maneira de testar a modificação de um runbook para `Enable-AzureRmAlias -Scope Process` usar os novos cmdlets é usando no início do runbook. Adicionando este comando ao seu runbook, o script pode ser executado sem alterações.

## <a name="after-migration-details"></a>Detalhes pós-migração

Depois que a migração estiver concluída, não tente iniciar runbooks usando módulos AzureRM na conta Automação. Também é recomendado não importar ou atualizar módulos AzureRM na conta. Considere a conta migrada para Az e opere apenas com módulos Az. 

Quando você cria uma nova conta de Automação, os módulos AzureRM existentes ainda são instalados. Você ainda pode atualizar os runbooks tutoriais com cmdlets AzureRM. No entanto, você não deve executar esses runbooks.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como usar módulos Az, consulte [Introdução ao módulo Az](/powershell/azure/get-started-azureps?view=azps-1.1.0).
