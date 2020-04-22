---
title: Solucionar problemas de recursos compartilhados no Azure Automation
description: Saiba como solucionar problemas e resolver problemas com recursos compartilhados da Azure Automation.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/12/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: e83c7074d252083329537e205666374705a31873
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733566"
---
# <a name="troubleshoot-shared-resources-in-azure-automation"></a>Solucionar problemas de recursos compartilhados no Azure Automation

Este artigo discute soluções para problemas que você pode encontrar ao usar [recursos compartilhados](../automation-intro.md#shared-resources) no Azure Automation.

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no trabalhador do runbook híbrido, consulte [Instalar o Módulo PowerShell do Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de Automação, você pode atualizar seus módulos para a versão mais recente usando [Como atualizar módulos Azure PowerShell no Azure Automation](../automation-update-azure-modules.md).

## <a name="modules"></a>Módulos

### <a name="scenario-a-module-is-stuck-during-import"></a><a name="module-stuck-importing"></a>Cenário: Um módulo fica preso durante a importação

#### <a name="issue"></a>Problema

Um módulo fica preso no estado de importação quando você está importando ou atualizando seus módulos de Automação Azure.

#### <a name="cause"></a>Causa

Uma vez que a importação de módulos PowerShell é um processo complexo de várias etapas, um módulo pode não importar corretamente e pode ficar preso em um estado transitório. Para saber mais sobre o processo de importação, consulte [Importar um Módulo PowerShell](/powershell/scripting/developer/module/importing-a-powershell-module#the-importing-process).

#### <a name="resolution"></a>Resolução

Para resolver esse problema, você deve remover o módulo que está preso no estado de importação usando o cmdlet [Remove-AzAutomationModule.](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationModule?view=azps-3.7.0) Você pode, em seguida, tente importar novamente o módulo.

```azurepowershell-interactive
Remove-AzAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

### <a name="scenario-azurerm-modules-are-stuck-during-import-after-an-update-attempt"></a><a name="update-azure-modules-importing"></a>Cenário: Módulos AzureRM ficam presos durante a importação após uma tentativa de atualização

#### <a name="issue"></a>Problema

Um banner com a seguinte mensagem permanece em sua conta depois de tentar atualizar seus módulos AzureRM:

```error
Azure modules are being updated
```

#### <a name="cause"></a>Causa

Há um problema conhecido com a atualização dos módulos AzureRM em uma conta de Automação que está em um grupo de recursos com um nome numérico começando com 0.

#### <a name="resolution"></a>Resolução

Para atualizar seus módulos AzureRM em sua conta de Automação, a conta deve estar em um grupo de recursos com um nome alfanumérico. Grupos de recursos com nomes numéricos que começam com 0 não podem atualizar módulos AzureRM no momento.

### <a name="scenario-module-fails-to-import-or-cmdlets-cant-be-executed-after-importing"></a><a name="module-fails-to-import"></a>Cenário: Falha de módulo importar ou cmdlets não pode ser executados após a importação

#### <a name="issue"></a>Problema

Um módulo não importa ou importa com sucesso, mas não são extraídos cmdlets.

#### <a name="cause"></a>Causa

Algumas razões comuns para que um módulo não pode importar com êxito à automação do Azure são:

* A estrutura não corresponde à estrutura que a Automação precisa.
* O módulo depende de outro módulo que não tenha sido implantado em sua conta de Automação.
* O módulo não tem suas dependências na pasta.
* O [cmdlet New-AzAutomationModule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationModule?view=azps-3.7.0) está sendo usado para carregar o módulo, e você não deu o caminho completo de armazenamento ou não carregou o módulo usando uma URL acessível ao público.

#### <a name="resolution"></a>Resolução

Use qualquer uma dessas soluções para corrigir o problema.

* Certifique-se de que o módulo segue o formato: ModuleName.zip -> ModuleName ou Número de Versão -> (ModuleName.psm1, ModuleName.psd1).
* Abra o arquivo **.psd1** e veja se o módulo tem alguma dependência. Se tiver, carregue esses módulos para a conta de Automação.
* Certifique-se de que todos os arquivos **.dll** referenciados estejam presentes na pasta do módulo.

### <a name="scenario-update-azuremoduleps1-suspends-when-updating-modules"></a><a name="all-modules-suspended"></a>Cenário: Update-AzureModule.ps1 é suspenso ao atualizar módulos

#### <a name="issue"></a>Problema

Ao usar o runbook [Update-AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) para atualizar seus módulos Azure, o processo de atualização do módulo está suspenso.

#### <a name="cause"></a>Causa

A configuração padrão para determinar quantos módulos são atualizados simultaneamente é de 10 ao usar **Update-AzureModule.ps1**. O processo de atualização é propenso a erros quando muitos módulos estão sendo atualizados ao mesmo tempo.

#### <a name="resolution"></a>Resolução

Não é comum que todos os módulos AzureRM ou Az sejam necessários na mesma conta de Automação. Recomenda-se importar apenas os módulos específicos que você precisa.

> [!NOTE]
> Evite importar o `Az.Automation` `AzureRM.Automation` inteiro ou módulo, que importa todos os módulos contidos.

Se o processo de atualização `SimultaneousModuleImportJobCount` for suspenso, adicione o parâmetro ao script **Update-AzureModules.ps1** e forneça um valor menor do que o padrão de 10. Se você implementar essa lógica, é recomendável começar com um valor de 3 ou 5. `SimultaneousModuleImportJobCount`é um parâmetro do manual do sistema **Update-AutomationAzureModulesForAccount** que é usado para atualizar módulos Azure. Se você fizer esse ajuste, o processo de atualização é mais longo, mas tem mais chances de ser concluído. O exemplo a seguir mostra o parâmetro e onde colocá-lo no runbook:

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

### <a name="scenario-youre-unable-to-create-or-update-a-run-as-account"></a><a name="unable-create-update"></a>Cenário: Você não pode criar ou atualizar uma conta Run As

#### <a name="issue"></a>Problema

Ao tentar criar ou atualizar uma conta Executar como, um erro semelhante à mensagem de erro a seguir é exibido:

```error
You do not have permissions to create…
```

#### <a name="cause"></a>Causa

Você não tem as permissões necessárias para criar ou atualizar a conta Executar como ou o recurso está bloqueado em um nível de grupo de recursos.

#### <a name="resolution"></a>Resolução

Para criar ou atualizar uma conta Run As, você deve ter [permissões apropriadas](../manage-runas-account.md#permissions) para os vários recursos usados pela conta Run As. 

Se o problema for por causa de uma trava, verifique se a fechadura pode ser removida. Em seguida, navegue até o recurso bloqueado no portal Azure, clique com o botão direito do mouse no bloqueio e clique **em Excluir**.

### <a name="scenario-you-receive-the-error-unable-to-find-an-entry-point-named-getperadapterinfo-in-dll-iplpapidll-when-executing-a-runbook"></a><a name="iphelper"></a>Cenário: Você recebe o erro "Não é possível encontrar um ponto de entrada chamado 'GetPerAdapterInfo' no DLL 'iplpapi.dll'" ao executar um runbook

#### <a name="issue"></a>Problema

Ao executar um runbook, você recebe a seguinte exceção:

```error
Unable to find an entry point named 'GetPerAdapterInfo' in DLL 'iplpapi.dll'
```

#### <a name="cause"></a>Causa

Esse erro provavelmente é causado por uma [conta execute as de forma](../manage-runas-account.md)incorreta configurada .

#### <a name="resolution"></a>Resolução

Certifique-se de que sua conta Executar as está configurada corretamente. Em seguida, verifique se você tem o código adequado em seu manual para autenticar com o Azure. O exemplo a seguir mostra um trecho de código para autenticar o Azure em um runbook usando uma conta run as.

```powershell
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
```

## <a name="next-steps"></a>Próximas etapas

Se você não vê o seu problema acima ou não consegue resolver o seu problema, tente um dos seguintes canais para obter suporte adicional:

* Obtenha respostas de especialistas do Azure através [do Azure Forums](https://azure.microsoft.com/support/forums/).
* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport), a conta oficial do Microsoft Azure para melhorar a experiência do cliente conectando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.
* Registrar um incidente de suporte do Azure. Vá ao site de suporte do [Azure](https://azure.microsoft.com/support/options/) e **selecione Obter suporte**.
