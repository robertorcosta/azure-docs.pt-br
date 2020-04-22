---
title: Gerencie pré-scripts e pós-scripts na implantação do Gerenciamento de Atualizações no Azure
description: Este artigo descreve como configurar e gerenciar pré-scripts e pós-scripts para implantações de atualização.
services: automation
ms.subservice: update-management
ms.date: 05/17/2019
ms.topic: conceptual
ms.openlocfilehash: 00cde5255f9c9a2baa7c7042ae2a8f73448da0ae
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679987"
---
# <a name="manage-pre-scripts-and-post-scripts"></a>Gerenciar pré-scripts e pós-scripts

Pré-scripts e pós-scripts são runbooks para executar em sua conta azure Automação antes (pré-tarefa) e depois (pós-tarefa) uma implantação de atualização. Pré-scripts e pós-scripts são executados no contexto azure, não localmente. Os pré-scripts são executados no início da implantação da atualização. Os scripts pós-roteirizam no final da implantação e após quaisquer reinicializações configuradas.

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no trabalhador do runbook híbrido, consulte [Instalar o Módulo PowerShell do Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de Automação, você pode atualizar seus módulos para a versão mais recente usando [Como atualizar módulos Azure PowerShell no Azure Automation](automation-update-azure-modules.md).

## <a name="pre-script-and-post-script-requirements"></a>Requisitos de pré-script e pós-script

Para que um runbook seja usado como pré-script ou pós-script, você deve importá-lo para sua conta de Automação e [publicar o runbook](manage-runbooks.md#publishing-a-runbook).

## <a name="pre-script-and-post-script-parameters"></a>Parâmetros de pré-script e pós-script

Quando você configura pré-scripts e pós-scripts, você pode passar em parâmetros como agendar um runbook. Os parâmetros são definidos no momento da criação da implantação de atualização. Pré-scripts e pós-scripts suportam os seguintes tipos:

* [char]
* [byte]
* [int]
* [long]
* [decimal]
* [single]
* [double]
* [DateTime]
* [string]

Os parâmetros de execução de pré-script e pós-script não suportam tipos booleanos, objetos ou arrays. Esses valores fazem com que os runbooks falhem. 

Se precisar de outro tipo de objeto, você poderá convertê-lo em outro tipo usando com sua própria lógica no runbook.

Além dos parâmetros padrão do `SoftwareUpdateConfigurationRunContext` runbook, o parâmetro (tipo string JSON) é fornecido. Se você definir o parâmetro em seu manual de pré-script ou pós-script, ele será automaticamente passado pela implantação da atualização. O parâmetro contém informações sobre a implantação da atualização, que é um subconjunto de informações retornadas pela [API De configurações de softwareUpdateconfigurations](/rest/api/automation/softwareupdateconfigurations/getbyname#updateconfiguration). As seções abaixo definem as propriedades associadas.

### <a name="softwareupdateconfigurationruncontext-properties"></a>Propriedades de SoftwareUpdateConfigurationRunContext

|Propriedade  |Descrição  |
|---------|---------|
|SoftwareUpdateConfigurationName     | O nome da configuração de atualização de software.        |
|SoftwareUpdateConfigurationRunId     | A identificação única para a corrida.        |
|SoftwareUpdateConfigurationSettings     | Uma coleção de propriedades relacionadas à configuração de atualização de software.         |
|SoftwareUpdateConfigurationSettings.operatingSystem     | Os sistemas operacionais direcionados para a implantação da atualização.         |
|SoftwareUpdateConfigurationSettings.duration     | A duração máxima da implantação `PT[n]H[n]M[n]S` da atualização é executada conforme ISO8601; também chamado de janela de manutenção.          |
|SoftwareUpdateConfigurationSettings.Windows     | Uma coleção de propriedades relacionadas a computadores Windows.         |
|SoftwareUpdateConfigurationSettings.Windows.excludedKbNumbers     | Uma lista de KBs que estão excluídos da implantação da atualização.        |
|SoftwareUpdateConfigurationSettings.Windows.includedUpdateClassifications     | Classificações de atualização selecionadas para a implantação da atualização.        |
|SoftwareUpdateConfigurationSettings.Windows.rebootSetting     | Configurações de reinicialização para a implantação da atualização.        |
|azureVirtualMachines     | Uma lista de ids de recursos para as VMs do Azure na implantação da atualização.        |
|nonAzureComputerNames|Uma lista dos computadores não-Azure FQDNs na implantação da atualização.|

A seguir há um exemplo de cadeia de caracteres JSON passada para o parâmetro **SoftwareUpdateConfigurationRunContext**:

```json
"SoftwareUpdateConfigurationRunContext":{
      "SoftwareUpdateConfigurationName":"sampleConfiguration",
      "SoftwareUpdateConfigurationRunId":"00000000-0000-0000-0000-000000000000",
      "SoftwareUpdateConfigurationSettings":{
         "operatingSystem":"Windows",
         "duration":"PT2H0M",
         "windows":{
            "excludedKbNumbers":[
               "168934",
               "168973"
            ],
            "includedUpdateClassifications":"Critical",
            "rebootSetting":"IfRequired"
         },
         "azureVirtualMachines":[
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresources/providers/Microsoft.Compute/virtualMachines/vm-01",
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresources/providers/Microsoft.Compute/virtualMachines/vm-02",
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresources/providers/Microsoft.Compute/virtualMachines/vm-03"
         ],
         "nonAzureComputerNames":[
            "box1.contoso.com",
            "box2.contoso.com"
         ]
      }
   }
```

Um exemplo completo com todas as propriedades pode ser encontrado em: [Obter configuração de atualização de software pelo nome](/rest/api/automation/softwareupdateconfigurations/getbyname#examples).

> [!NOTE]
> O `SoftwareUpdateConfigurationRunContext` objeto pode conter entradas duplicadas para máquinas. Isso pode fazer com que pré-scripts e pós-scripts seja executado várias vezes na mesma máquina. Para contornar esse comportamento, use `Sort-Object -Unique` para selecionar apenas nomes vm exclusivos.

## <a name="using-a-pre-script-or-post-script-in-a-deployment"></a>Usando um pré-script ou pós-script em uma implantação

Para usar um pré-script ou pós-script em uma implantação de atualização, comece criando uma implantação de atualização. Selecione **Pré-scripts + Pós-scripts**. Essa ação abre a página **Selecionar Pré-scripts + Pós-scripts**.

![Selecionar scripts](./media/pre-post-scripts/select-scripts.png)

Selecione o script que deseja usar. Neste exemplo, usamos o runbook **UpdateManagement-TurnOnVms.** Quando você seleciona o runbook, a **página Configurar script** é aberta. Selecione **Pré-Script**e selecione **OK**.

Repita esse processo para o script **UpdateManagement-TurnOffVms**. Mas quando você escolher o **tipo Script,** selecione **Post-Script**.

A seção **Itens selecionados** agora mostra os dois scripts selecionados. Um é um pré-script e o outro é um pós-script:

![Itens selecionados](./media/pre-post-scripts/selected-items.png)

Termine de configurar sua implantação de atualização.

Quando sua implantação de atualização estiver concluída, você pode ir para **as implantações de atualização** para visualizar os resultados. Como você pode ver, o status é fornecido para o pré-script e pós-script:

![Atualizar resultados](./media/pre-post-scripts/update-results.png)

Ao selecionar a execução da implantação da atualização, você é mostrado detalhes adicionais de pré-scripts e pós-scripts. Um link para a fonte do script no momento da execução é fornecido.

![Resultados da execução da implantação](./media/pre-post-scripts/deployment-run.png)

es em seu script.

## <a name="stopping-a-deployment"></a>Parando uma implantação

Se você quiser interromper uma implantação com base em um pré-script, você deve [lançar](automation-runbook-execution.md#throw) uma exceção. Se você não fizer isso, a implantação e o pós-script ainda serão executados. O seguinte trecho de código mostra como abrir uma exceção.

```powershell
#In this case, we want to terminate the patch job if any run fails.
#This logic might not hold for all cases - you might want to allow success as long as at least 1 run succeeds
foreach($summary in $finalStatus)
{
    if ($summary.Type -eq "Error")
    {
        #We must throw in order to fail the patch deployment.
        throw $summary.Summary
    }
}
```



## <a name="interacting-with-machines"></a>Interagindo com máquinas

Pré-scripts e pós-tarefas são executados como runbooks em sua conta de Automação e não diretamente nas máquinas em sua implantação. Pré-tarefas e pós-tarefas também são executadas no contexto do Azure e não têm acesso a máquinas não-Azure. As seções a seguir mostram como você pode interagir diretamente com as máquinas, sejam elas VMs Azure ou máquinas não-Azure.

### <a name="interact-with-azure-machines"></a>Interaja com máquinas Azure

Pré-tarefas e pós-tarefas são executadas como runbooks e não são executadas nativamente em suas VMs azure em sua implantação. Para interagir com suas VMs do Azure, você deve ter os seguintes itens:

* Uma conta Executar como
* Um runbook que você quer executar

Para interagir com as máquinas Azure, você deve usar o cmdlet [Invoke-AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand?view=azps-3.7.0) para interagir com suas VMs azure. Para um exemplo de como fazer isso, consulte o exemplo do runbook [Update Management – execute script with Run command](https://gallery.technet.microsoft.com/Update-Management-Run-40f470dc).

### <a name="interact-with-non-azure-machines"></a>Interaja com máquinas não-Azure

Pré-tarefas e pós-tarefas são executadas no contexto do Azure e não têm acesso a máquinas não-Azure. Para interagir com as máquinas não-Azure, você deve ter os seguintes itens:

* Uma conta Executar como
* Um Hybrid Runbook Worker instalado no computador
* Um runbook que deseja executar localmente
* Um runbook dos pais

Para interagir com máquinas não-Azure, um runbook pai é executado no contexto Azure. Este runbook chama um manual infantil com o [cmdlet Start-AzAutomationRunbook.](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.7.0) É necessário especificar o parâmetro `RunOn` e fornecer o nome do Hybrid Runbook Worker para o script ser executado. Consulte o exemplo do runbook [Update Management – execute script localmente](https://gallery.technet.microsoft.com/Update-Management-Run-6949cc44).

## <a name="aborting-patch-deployment"></a>Abortando a implantação de patch

Se o seu pré-script retornar um erro, você pode querer abortar sua implantação. Para fazer isso, você deve [jogar](/powershell/module/microsoft.powershell.core/about/about_throw) um erro em seu script para qualquer lógica que constitua um fracasso.

```powershell
if (<My custom error logic>)
{
    #Throw an error to fail the patch deployment.
    throw "There was an error, abort deployment"
}
```

## <a name="samples"></a>Exemplos

Amostras de pré-scripts e pós-scripts podem ser encontradas na [Script Center Gallery](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B0%5D.Text=Windows%20Azure&f%5B1%5D.Type=SubCategory&f%5B1%5D.Value=WindowsAzure_automation&f%5B1%5D.Text=Automation&f%5B2%5D.Type=SearchText&f%5B2%5D.Value=update%20management&f%5B3%5D.Type=Tag&f%5B3%5D.Value=Patching&f%5B3%5D.Text=Patching&f%5B4%5D.Type=ProgrammingLanguage&f%5B4%5D.Value=PowerShell&f%5B4%5D.Text=PowerShell) e na [PowerShell Gallery](https://www.powershellgallery.com/packages?q=Tags%3A%22UpdateManagement%22+Tags%3A%22Automation%22), ou você pode importá-las através do portal Azure. Para fazer isso, em sua conta de Automação, em **Automação de Processos,** selecione **Runbooks Gallery**. Use **Gerenciamento de Atualizações** para o filtro.

![Lista de galerias](./media/pre-post-scripts/runbook-gallery.png)

Ou você pode procurá-los pelo nome do script, como mostrado na lista a seguir:

* Gerenciamento de Atualizações – Ligar VMs
* Gerenciamento de Atualizações – Desligar VMs
* Gerenciamento de Atualizações – Executar script localmente
* Gerenciamento de Atualizações – Modelo para scripts pré/pós
* Gerenciamento de Atualizações – Executar script com comando de execução

> [!IMPORTANT]
> Depois de importar os runbooks, você deve publicá-los antes que eles possam ser usados. Para fazer isso, encontre o manual na conta de Automação, selecione **Editar**e selecione **Publicar**.

As amostras são todas baseadas no modelo básico definido no exemplo a seguir. Este modelo pode ser usado para criar seu próprio runbook para usar com pré-scripts e pós-scripts. A lógica necessária para autenticar com o `SoftwareUpdateConfigurationRunContext` Azure e manusear o parâmetro está incluída.

```powershell
<#
.SYNOPSIS
 Barebones script for Update Management Pre/Post

.DESCRIPTION
  This script is intended to be run as a part of Update Management pre/post-scripts.
  It requires a RunAs account.

.PARAMETER SoftwareUpdateConfigurationRunContext
  This is a system variable which is automatically passed in by Update Management during a deployment.
#>

param(
    [string]$SoftwareUpdateConfigurationRunContext
)
#region BoilerplateAuthentication
#This requires a RunAs account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Add-AzAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Select-AzSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID
#endregion BoilerplateAuthentication

#If you wish to use the run context, it must be converted from JSON
$context = ConvertFrom-Json  $SoftwareUpdateConfigurationRunContext
#Access the properties of the SoftwareUpdateConfigurationRunContext
$vmIds = $context.SoftwareUpdateConfigurationSettings.AzureVirtualMachines | Sort-Object -Unique
$runId = $context.SoftwareUpdateConfigurationRunId

Write-Output $context

#Example: How to create and write to a variable using the pre-script:
<#
#Create variable named after this run so it can be retrieved
New-AzAutomationVariable -ResourceGroupName $ResourceGroup –AutomationAccountName $AutomationAccount –Name $runId -Value "" –Encrypted $false
#Set value of variable
Set-AutomationVariable –Name $runId -Value $vmIds
#>

#Example: How to retrieve information from a variable set during the pre-script
<#
$variable = Get-AutomationVariable -Name $runId
#>
```

> [!NOTE]
> Para runbooks PowerShell não `Add-AzAccount` gráficos e `Add-AzureRMAccount` são pseudônimos para [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0). Você pode usar esses cmdlets ou atualizar [seus módulos](automation-update-azure-modules.md) em sua conta de Automação para as versões mais recentes. Você pode precisar atualizar seus módulos mesmo se você acabou de criar uma nova conta de Automação.

## <a name="next-steps"></a>Próximas etapas

Acesse o tutorial a seguir para saber como gerenciar atualizações para suas máquinas virtuais do Windows:

> [!div class="nextstepaction"]
> [Gerenciar atualizações e patches para VMs do Microsoft Azure](automation-tutorial-update-management.md)