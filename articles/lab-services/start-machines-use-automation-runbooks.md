---
title: Iniciar máquinas usando runbooks de automação em Azure DevTest Labs
description: Aprenda a iniciar máquinas virtuais em um laboratório no Azure DevTest Labs usando os runbooks da Azure Automation.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 9bb97a73b7ca570ca122323e8e9c5a70c9348b15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76166305"
---
# <a name="start-virtual-machines-in-a-lab-in-order-by-using-azure-automation-runbooks"></a>Inicie máquinas virtuais em um laboratório em ordem usando runbooks da Azure Automation
O recurso de [partida automática](devtest-lab-set-lab-policy.md#set-autostart) do DevTest Labs permite configurar VMs para iniciar automaticamente em um horário especificado. No entanto, esse recurso não suporta máquinas para iniciar em uma ordem específica. Existem vários cenários em que esse tipo de automação seria útil.  Um cenário é onde uma VM Jumpbox dentro de um laboratório precisa ser iniciada primeiro, antes das outras VMs, já que o Jumpbox é usado como ponto de acesso às outras VMs.  Este artigo mostra como configurar uma conta do Azure Automation com um runbook powershell que executa um script. O script usa tags em VMs no laboratório para permitir que você controle a ordem de inicialização sem ter que alterar o script.

## <a name="setup"></a>Instalação
Neste exemplo, as VMs no laboratório precisam ter a tag **StartupOrder** adicionada com o valor apropriado (0,1,2, etc.). Designe qualquer máquina que não precise ser iniciada como -1.

## <a name="create-an-azure-automation-account"></a>Criar uma conta de Automação do Azure
Crie uma conta de Automação Azure seguindo instruções [neste artigo](../automation/automation-create-standalone-account.md). Escolha a opção **Executar como contas** ao criar a conta. Uma vez criada a conta de automação, abra a página **Módulos** e selecione **Atualizar módulos Azure** na barra de menus. Os módulos padrão são várias versões antigas e sem a atualização o script pode não funcionar.

## <a name="add-a-runbook"></a>Adicionar um runbook
Agora, para adicionar um runbook à conta de automação, **selecione Runbooks** no menu esquerdo. Selecione **Adicionar um runbook** no menu e siga as instruções para [criar um runbook PowerShell](../automation/automation-first-runbook-textual-powershell.md).

## <a name="powershell-script"></a>Script do PowerShell
O script a seguir leva o nome da assinatura, o nome do laboratório como parâmetros. O fluxo do script é para obter todas as VMs no laboratório e, em seguida, analisar as informações da tag para criar uma lista dos nomes de VM e sua ordem de inicialização. O script percorre as VMs em ordem e inicia as VMs. Se houver várias VMs em um número de pedido específico, elas serão iniciadas de forma assíncrona usando trabalhos powershell. Para aquelas VMs que não possuem uma tag, definir o valor de inicialização para ser o último (10), eles serão iniciados por último, por padrão.  Se o laboratório não quiser que a VM seja iniciada automaticamente, defina o valor da tag para 11 e ele será ignorado.

```powershell
#Requires -Version 3.0
#Requires -Module AzureRM.Resources

param
(
    [Parameter(Mandatory=$false, HelpMessage="Name of the subscription that has the lab")]
    [string] $SubscriptionName,

    [Parameter(Mandatory=$false, HelpMessage="Lab name")]
    [string] $LabName
)

# Connect and add the appropriate subscription
$Conn = Get-AutomationConnection -Name AzureRunAsConnection

Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationID $Conn.ApplicationId -Subscription $SubscriptionName -CertificateThumbprint $Conn.CertificateThumbprint

# Find the lab
$dtLab = Find-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceNameEquals $LabName

# Get the VMs
$dtlAllVms = New-Object System.Collections.ArrayList
$AllVMs = Get-AzResource -ResourceId "$($dtLab.ResourceId)/virtualmachines" -ApiVersion 2016-05-15

# Get the StartupOrder tag, if missing set to be run last (10)
ForEach ($vm in $AllVMs) {
    if ($vm.Tags) {
        if ($vm.Tags['StartupOrder']) {
            $startupValue = $vm.Tags['StartupOrder']
        } else {
            $startupValue = 10
        }
        } else {
            $startupValue = 10
        }
        $dtlAllVms.Add(@{$vm.Name = $startupValue}) > $null
}

# Setup for the async multiple vm start

# Save profile
$profilePath = Join-Path $env:Temp "profile.json"
If (Test-Path $profilePath){
    Remove-Item $profilePath
}
Save-AzContext -Path $profilePath

# Job to start VMs asynch
$startVMBlock = {
    Param($devTestLab,$vmToStart,$profilePath)
    Import-AzContext -Path ($profilePath)
    Invoke-AzResourceAction `
        -ResourceId "$($devTestLab.ResourceId)/virtualmachines/$vmToStart" `
        -Action Start `
        -Force
    Write-Output "Started: $vmToStart"
}

$current = 0
# Start in order from 0 to 10

While ($current -le 10) {
# Get the VMs in the current stage
    $tobeStarted = $null
    $tobeStarted = $dtlAllVms | Where-Object { $_.Values -eq $current}
    if ($tobeStarted.Count -eq 1) {
        # Run sync – jobs not necessary for a single VM
        $returnStatus = Invoke-AzResourceAction `
                -ResourceId "$($dtLab.ResourceId)/virtualmachines/$($tobeStarted.Keys)" `
                -Action Start `
                -Force
        Write-Output "$($tobeStarted.Keys) status: $($returnStatus.status)"
    } elseif ($tobeStarted.Count -gt 1) {
        # Start multiple VMs async
        $jobs = @()
        Write-Output "Start Jobs start: $(Get-Date)"
        
        # Jobs
        $jobs += Start-Job -ScriptBlock $startVMBlock -ArgumentList $dtLab, $($singlevm.Keys), $profilePath
        Write-Output "Start Jobs end: $(Get-Date)"
    }

    # Get results from all jobs
    if($jobs.Count -ne 0) {
        Write-Output "Receive Jobs start: $(Get-Date)"
        foreach ($job in $jobs){
            $jobResult = Receive-Job -Job $job -Wait | Write-Output
        }
        Remove-Job -Job $jobs -Force
    }
    else
    {
        Write-Output "Information: No jobs available"
    }
}
```

## <a name="create-a-schedule"></a>Crie um cronograma
Para que esse script seja executado diariamente, [crie um cronograma](../automation/shared-resources/schedules.md#creating-a-schedule) na conta de automação. Uma vez que o cronograma é criado, [vincule-o ao runbook](../automation/shared-resources/schedules.md#linking-a-schedule-to-a-runbook). 

Em uma situação em grande escala, onde há várias assinaturas com vários laboratórios, armazene as informações do parâmetro em um arquivo para diferentes laboratórios e passe o arquivo para o script em vez dos parâmetros individuais. O script precisaria ser modificado, mas a execução do núcleo seria a mesma. Embora essa amostra use a Automação Azure para executar o script PowerShell, existem outras opções, como usar uma tarefa em um pipeline de compilação/lançamento.

## <a name="next-steps"></a>Próximas etapas
Veja o artigo a seguir para saber mais sobre a Automação Azure: [Uma introdução ao Azure Automation](../automation/automation-intro.md).
