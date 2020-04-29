---
title: Solucionar problemas de recursos compartilhados na automação do Azure
description: Saiba como solucionar problemas e resolver questões com recursos compartilhados da automação do Azure.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/12/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: e83c7074d252083329537e205666374705a31873
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81733566"
---
# <a name="troubleshoot-shared-resources-in-azure-automation"></a>Solucionar problemas de recursos compartilhados na automação do Azure

Este artigo aborda soluções para problemas que você pode encontrar ao usar [recursos compartilhados](../automation-intro.md#shared-resources) na automação do Azure.

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no seu Hybrid Runbook Worker, consulte [instalar o módulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de automação, você pode atualizar seus módulos para a versão mais recente usando [como atualizar os módulos de Azure PowerShell na automação do Azure](../automation-update-azure-modules.md).

## <a name="modules"></a>Módulos

### <a name="scenario-a-module-is-stuck-during-import"></a><a name="module-stuck-importing"></a>Cenário: um módulo está preso durante A importação

#### <a name="issue"></a>Problema

Um módulo está preso no estado de importação quando você está importando ou atualizando seus módulos de automação do Azure.

#### <a name="cause"></a>Causa

Como a importação de módulos do PowerShell é um processo complexo de várias etapas, um módulo pode não ser importado corretamente e pode estar preso em um estado transitório. Para saber mais sobre o processo de importação, consulte [importando um módulo do PowerShell](/powershell/scripting/developer/module/importing-a-powershell-module#the-importing-process).

#### <a name="resolution"></a>Resolução

Para resolver esse problema, você deve remover o módulo que está preso no estado de importação usando o cmdlet [Remove-AzAutomationModule](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationModule?view=azps-3.7.0) . Você pode, em seguida, tente importar novamente o módulo.

```azurepowershell-interactive
Remove-AzAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

### <a name="scenario-azurerm-modules-are-stuck-during-import-after-an-update-attempt"></a><a name="update-azure-modules-importing"></a>Cenário: os módulos AzureRM são presos durante a importação após uma tentativa de atualização

#### <a name="issue"></a>Problema

Uma faixa com a seguinte mensagem permanece em sua conta depois de tentar atualizar os módulos do AzureRM:

```error
Azure modules are being updated
```

#### <a name="cause"></a>Causa

Há um problema conhecido com a atualização dos módulos AzureRM em uma conta de automação que está em um grupo de recursos com um nome numérico começando com 0.

#### <a name="resolution"></a>Resolução

Para atualizar os módulos do AzureRM na sua conta de automação, a conta deve estar em um grupo de recursos com um nome alfanumérico. Os grupos de recursos com nomes numéricos que começam com 0 não podem atualizar os módulos AzureRM no momento.

### <a name="scenario-module-fails-to-import-or-cmdlets-cant-be-executed-after-importing"></a><a name="module-fails-to-import"></a>Cenário: Falha de módulo importar ou cmdlets não pode ser executados após a importação

#### <a name="issue"></a>Problema

Um módulo não é importado ou importado com êxito, mas nenhum cmdlet é extraído.

#### <a name="cause"></a>Causa

Algumas razões comuns para que um módulo não pode importar com êxito à automação do Azure são:

* A estrutura não corresponde à estrutura necessária para a automação.
* O módulo depende de outro módulo que não tenha sido implantado em sua conta de Automação.
* O módulo não tem suas dependências na pasta.
* O cmdlet [New-AzAutomationModule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationModule?view=azps-3.7.0) está sendo usado para carregar o módulo e você não recebeu o caminho de armazenamento completo ou não carregou o módulo usando uma URL acessível publicamente.

#### <a name="resolution"></a>Resolução

Use qualquer uma dessas soluções para corrigir o problema.

* Certifique-se de que o módulo segue o formato: ModuleName. zip-> Móduloname ou número de versão-> (ModuleName. psm1, ModuleName. psd1).
* Abra o arquivo **. psd1** e veja se o módulo tem alguma dependência. Se tiver, carregue esses módulos para a conta de Automação.
* Certifique-se de que todos os arquivos **. dll** referenciados estão presentes na pasta do módulo.

### <a name="scenario-update-azuremoduleps1-suspends-when-updating-modules"></a><a name="all-modules-suspended"></a>Cenário: Update-AzureModule. ps1 suspende ao atualizar módulos

#### <a name="issue"></a>Problema

Ao usar o runbook [Update-AzureModule. ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) para atualizar seus módulos do Azure, o processo de atualização do módulo é suspenso.

#### <a name="cause"></a>Causa

A configuração padrão para determinar quantos módulos são atualizados simultaneamente é 10 ao usar **Update-AzureModule. ps1**. O processo de atualização é propenso a erros quando muitos módulos estão sendo atualizados ao mesmo tempo.

#### <a name="resolution"></a>Resolução

Não é comum que todos os módulos AzureRM ou AZ sejam necessários na mesma conta de automação. É recomendável importar apenas os módulos específicos de que você precisa.

> [!NOTE]
> Evite importar todo `Az.Automation` o módulo `AzureRM.Automation` ou, que importa todos os módulos contidos.

Se o processo de atualização suspender, adicione `SimultaneousModuleImportJobCount` o parâmetro ao script **Update-AzureModules. ps1** e forneça um valor mais baixo do que o padrão de 10. Se você implementar essa lógica, é recomendável começar com um valor de 3 ou 5. `SimultaneousModuleImportJobCount`é um parâmetro do runbook do sistema **Update-AutomationAzureModulesForAccount** que é usado para atualizar os módulos do Azure. Se você fizer esse ajuste, o processo de atualização será executado por mais tempo, mas terá uma chance melhor de concluir. O exemplo a seguir mostra o parâmetro e onde colocá-lo no runbook:

 ```powershell
         $Body = @"
            {
               "properties":{
               "runbook":{
                   "name":"Update-AutomationAzureModulesForAccount"
               },
               "parameters":{
                    ...
                    "SimultaneousModuleImportJobCount":"3",
                    ... 
               }
              }
           }
"@
```

## <a name="run-as-accounts"></a>Contas Executar como

### <a name="scenario-youre-unable-to-create-or-update-a-run-as-account"></a><a name="unable-create-update"></a>Cenário: não é possível criar ou atualizar uma conta Executar como

#### <a name="issue"></a>Problema

Ao tentar criar ou atualizar uma conta Executar como, um erro semelhante à mensagem de erro a seguir é exibido:

```error
You do not have permissions to create…
```

#### <a name="cause"></a>Causa

Você não tem as permissões necessárias para criar ou atualizar a conta Executar como ou o recurso está bloqueado em um nível de grupo de recursos.

#### <a name="resolution"></a>Resolução

Para criar ou atualizar uma conta Executar como, você deve ter [as permissões](../manage-runas-account.md#permissions) apropriadas para os vários recursos usados pela conta Executar como. 

Se o problema for devido a um bloqueio, verifique se o bloqueio pode ser removido. Em seguida, navegue até o recurso bloqueado no portal do Azure, clique com o botão direito do mouse no bloqueio e clique em **excluir**.

### <a name="scenario-you-receive-the-error-unable-to-find-an-entry-point-named-getperadapterinfo-in-dll-iplpapidll-when-executing-a-runbook"></a><a name="iphelper"></a>Cenário: você recebe o erro "não foi possível encontrar um ponto de entrada chamado ' GetPerAdapterInfo ' na DLL ' iplpapi. dll '" ao executar um runbook

#### <a name="issue"></a>Problema

Ao executar um runbook, você receberá a seguinte exceção:

```error
Unable to find an entry point named 'GetPerAdapterInfo' in DLL 'iplpapi.dll'
```

#### <a name="cause"></a>Causa

Esse erro é provavelmente causado por uma [conta Executar como](../manage-runas-account.md)configurada incorretamente.

#### <a name="resolution"></a>Resolução

Verifique se a conta Executar como está configurada corretamente. Em seguida, verifique se você tem o código adequado em seu runbook para autenticar com o Azure. O exemplo a seguir mostra um trecho de código para autenticar no Azure em um runbook usando uma conta Executar como.

```powershell
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
```

## <a name="next-steps"></a>Próximas etapas

Se você não encontrar o problema acima ou não conseguir resolver o problema, tente um dos seguintes canais para obter suporte adicional:

* Obtenha respostas de especialistas do Azure por meio dos [fóruns do Azure](https://azure.microsoft.com/support/forums/).
* Conecte- [@AzureSupport](https://twitter.com/azuresupport)se com o, a conta de Microsoft Azure oficial para melhorar a experiência do cliente conectando a Comunidade do Azure aos recursos certos: respostas, suporte e especialistas.
* Registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **obter suporte**.
