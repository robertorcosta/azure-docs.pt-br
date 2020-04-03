---
title: Atualização automática do serviço de mobilidade na recuperação do site do Azure
description: Visão geral da atualização automática do serviço de mobilidade ao replicar VMs do Azure usando o Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/02/2020
ms.author: rajanaki
ms.openlocfilehash: 67298ecf0c17feee2d36bb8774cae37b1ca81381
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618970"
---
# <a name="automatic-update-of-the-mobility-service-in-azure-to-azure-replication"></a>Atualização automática do serviço de mobilidade na replicação Azure-to-Azure

O Azure Site Recovery usa uma cadência de versão mensal para corrigir quaisquer problemas e melhorar os recursos existentes ou adicionar novos. Para permanecer atualizado com o serviço, você deve planejar a implantação de patches a cada mês. Para evitar a sobrecarga associada a cada atualização, você pode permitir que a Recuperação do Site gerencie atualizações de componentes.

Como mencionado na [arquitetura de recuperação de desastres Azure-to-Azure,](azure-to-azure-architecture.md)o serviço de mobilidade está instalado em todas as máquinas virtuais (VMs) do Azure que têm a replicação ativada de uma região do Azure para outra. Quando você usa atualizações automáticas, cada nova versão atualiza a extensão do serviço Mobility.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="how-automatic-updates-work"></a>Como funcionam as atualizações automáticas

Quando você usa o Site Recovery para gerenciar atualizações, ele implanta um runbook global (usado pelos serviços do Azure) através de uma conta de automação, criada na mesma assinatura do cofre. Cada cofre usa uma conta de automação. Para cada VM em um cofre, o manual verifica as atualizações automáticas ativas. Se uma versão mais recente da extensão do serviço Mobility estiver disponível, a atualização será instalada.

O cronograma padrão do runbook ocorre diariamente às 12:00 am no fuso horário da geografia da VM replicada. Você também pode alterar o cronograma do runbook através da conta de automação.

> [!NOTE]
> Começando com [update rollup 35](site-recovery-whats-new.md#updates-march-2019), você pode escolher uma conta de automação existente para usar para atualizações. Antes da atualização do Rollup 35, o Site Recovery criou a conta de automação por padrão. Você só pode selecionar essa opção quando habilitar a replicação de uma VM. Ele não está disponível para uma VM que já tem a replicação ativada. A configuração selecionada se aplica a todas as VMs do Azure protegidas no mesmo cofre.

Ligar atualizações automáticas não requer uma reinicialização das Suas VMs do Azure ou afeta a replicação contínua.

O faturamento do trabalho na conta de automação é baseado no número de minutos de tempo de execução de trabalho usados em um mês. A execução do trabalho leva alguns segundos para cerca de um minuto por dia e é coberta como unidades livres. Por padrão, 500 minutos são incluídos como unidades gratuitas para uma conta de automação, conforme mostrado na tabela a seguir:

| Unidades gratuitas incluídas (a cada mês) | Price |
|---|---|
| Tempo de execução do trabalho 500 minutos | 0,14/minuto

## <a name="enable-automatic-updates"></a>Habilitar as atualizações automáticas

Existem várias maneiras pelas quais o Site Recovery pode gerenciar as atualizações de extensão:

- [Gerenciar como parte da etapa de replicação de habilitação](#manage-as-part-of-the-enable-replication-step)
- [Ativar/desativar as configurações de atualização de dentro do cofre](#toggle-the-extension-update-settings-inside-the-vault)
- [Gerenciar atualizações manualmente](#manage-updates-manually)

### <a name="manage-as-part-of-the-enable-replication-step"></a>Gerenciar como parte da etapa de replicação de habilitação

Quando você habilita a replicação de uma VM a [partir da exibição da VM](azure-to-azure-quickstart.md) ou [do cofre de serviços de recuperação,](azure-to-azure-how-to-enable-replication.md)você pode permitir que o Site Recovery gerencie atualizações para a extensão de Recuperação do Site ou gerencie-a manualmente.

:::image type="content" source="./media/azure-to-azure-autoupdate/enable-rep.png" alt-text="Configurações de extensão":::

### <a name="toggle-the-extension-update-settings-inside-the-vault"></a>Ativar/desativar as configurações de atualização de dentro do cofre

1. No cofre dos Serviços de Recuperação, vá para **Gerenciar** > a**infra-estrutura de recuperação do site**.
1. Em 'Configurações de**atualização de extensão de** > máquinas >  **virtuais' do Azure,****permita que a recuperação do site seja gerenciada,** selecione **Em**.

   Para gerenciar a extensão manualmente, **selecione Off**.

1. Clique em **Salvar**.

:::image type="content" source="./media/azure-to-azure-autoupdate/vault-toggle.png" alt-text="Configurações de atualização de extensão":::

> [!IMPORTANT]
> Quando você escolhe **Permitir que a recuperação do site seja gerenciada,** a configuração é aplicada a todas as VMs no cofre.

> [!NOTE]
> Qualquer opção notifica você da conta de automação usada para gerenciar atualizações. Se você estiver usando esse recurso em um cofre pela primeira vez, uma nova conta de automação será criada por padrão. Alternativamente, você pode personalizar a configuração e escolher uma conta de automação existente. Todos os taks subseqüentes para habilitar a replicação no mesmo cofre usarão a conta de automação criada anteriormente. Atualmente, o menu suspenso listará apenas contas de automação que estão no mesmo Grupo de Recursos do cofre.

> [!IMPORTANT]
> O script a seguir precisa ser executado no contexto de uma conta de automação.
Para uma conta de automação personalizada, use o seguinte script:

```azurepowershell
param(
    [Parameter(Mandatory=$true)]
    [String] $VaultResourceId,

    [Parameter(Mandatory=$true)]
    [ValidateSet("Enabled",'Disabled')]
    [Alias("Enabled or Disabled")]
    [String] $AutoUpdateAction,

    [Parameter(Mandatory=$false)]
    [String] $AutomationAccountArmId
)

$SiteRecoveryRunbookName = "Modify-AutoUpdateForVaultForPatner"
$TaskId = [guid]::NewGuid().ToString()
$SubscriptionId = "00000000-0000-0000-0000-000000000000"
$AsrApiVersion = "2018-01-10"
$RunAsConnectionName = "AzureRunAsConnection"
$ArmEndPoint = "https://management.azure.com"
$AadAuthority = "https://login.windows.net/"
$AadAudience = "https://management.core.windows.net/"
$AzureEnvironment = "AzureCloud"
$Timeout = "160"

function Throw-TerminatingErrorMessage
{
        Param
    (
        [Parameter(Mandatory=$true)]
        [String]
        $Message
        )

    throw ("Message: {0}, TaskId: {1}.") -f $Message, $TaskId
}

function Write-Tracing
{
        Param
    (
        [Parameter(Mandatory=$true)]
        [ValidateSet("Informational", "Warning", "ErrorLevel", "Succeeded", IgnoreCase = $true)]
                [String]
        $Level,

        [Parameter(Mandatory=$true)]
        [String]
        $Message,

            [Switch]
        $DisplayMessageToUser
        )

    Write-Output $Message

}

function Write-InformationTracing
{
        Param
    (
        [Parameter(Mandatory=$true)]
        [String]
        $Message
        )

    Write-Tracing -Message $Message -Level Informational -DisplayMessageToUser
}

function ValidateInput()
{
    try
    {
        if(!$VaultResourceId.StartsWith("/subscriptions", [System.StringComparison]::OrdinalIgnoreCase))
        {
            $ErrorMessage = "The vault resource id should start with /subscriptions."
            throw $ErrorMessage
        }

        $Tokens = $VaultResourceId.SubString(1).Split("/")
        if(!($Tokens.Count % 2 -eq 0))
        {
            $ErrorMessage = ("Odd Number of tokens: {0}." -f $Tokens.Count)
            throw $ErrorMessage
        }

        if(!($Tokens.Count/2 -eq 4))
        {
            $ErrorMessage = ("Invalid number of resource in vault ARM id expected:4, actual:{0}." -f ($Tokens.Count/2))
            throw $ErrorMessage
        }

        if($AutoUpdateAction -ieq "Enabled" -and [string]::IsNullOrEmpty($AutomationAccountArmId))
        {
            $ErrorMessage = ("The automation account ARM id should not be null or empty when AutoUpdateAction is enabled.")
            throw $ErrorMessage
        }
    }
    catch
    {
        $ErrorMessage = ("ValidateInput failed with [Exception: {0}]." -f $_.Exception)
        Write-Tracing -Level ErrorLevel -Message $ErrorMessage -DisplayMessageToUser
        Throw-TerminatingErrorMessage -Message $ErrorMessage
    }
}

function Initialize-SubscriptionId()
{
    try
    {
        $Tokens = $VaultResourceId.SubString(1).Split("/")

        $Count = 0
                $ArmResources = @{}
        while($Count -lt $Tokens.Count)
        {
            $ArmResources[$Tokens[$Count]] = $Tokens[$Count+1]
            $Count = $Count + 2
        }

                return $ArmResources["subscriptions"]
    }
    catch
    {
        Write-Tracing -Level ErrorLevel -Message ("Initialize-SubscriptionId: failed with [Exception: {0}]." -f $_.Exception) -DisplayMessageToUser
        throw
    }
}

function Invoke-InternalRestMethod($Uri, $Headers, [ref]$Result)
{
    $RetryCount = 0
    $MaxRetry = 3
    do
    {
        try
        {
            $ResultObject = Invoke-RestMethod -Uri $Uri -Headers $Headers
            ($Result.Value) += ($ResultObject)
            break
        }
        catch
        {
            Write-InformationTracing ("Retry Count: {0}, Exception: {1}." -f $RetryCount, $_.Exception)
            $RetryCount++
            if(!($RetryCount -le $MaxRetry))
            {
                throw
            }

            Start-Sleep -Milliseconds 2000
        }
    }while($true)
}

function Invoke-InternalWebRequest($Uri, $Headers, $Method, $Body, $ContentType, [ref]$Result)
{
    $RetryCount = 0
    $MaxRetry = 3
    do
    {
        try
        {
            $ResultObject = Invoke-WebRequest -Uri $UpdateUrl -Headers $Header -Method 'PATCH' `
                -Body $InputJson  -ContentType "application/json" -UseBasicParsing
            ($Result.Value) += ($ResultObject)
            break
        }
        catch
        {
            Write-InformationTracing ("Retry Count: {0}, Exception: {1}." -f $RetryCount, $_.Exception)
            $RetryCount++
            if(!($RetryCount -le $MaxRetry))
            {
                throw
            }

            Start-Sleep -Milliseconds 2000
        }
    }while($true)
}

function Get-Header([ref]$Header, $AadAudience, $AadAuthority, $RunAsConnectionName){
    try
    {
        $RunAsConnection = Get-AutomationConnection -Name $RunAsConnectionName
        $TenantId = $RunAsConnection.TenantId
        $ApplicationId = $RunAsConnection.ApplicationId
        $CertificateThumbprint = $RunAsConnection.CertificateThumbprint
        $Path = "cert:\CurrentUser\My\{0}" -f $CertificateThumbprint
        $Secret = Get-ChildItem -Path $Path
        $ClientCredential = New-Object Microsoft.IdentityModel.Clients.ActiveDirectory.ClientAssertionCertificate(
                $ApplicationId,
                $Secret)

        # Trim the forward slash from the AadAuthority if it exist.
        $AadAuthority = $AadAuthority.TrimEnd("/")
        $AuthContext = New-Object Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext(
            "{0}/{1}" -f $AadAuthority, $TenantId )
        $AuthenticationResult = $authContext.AcquireToken($AadAudience, $Clientcredential)
        $Header.Value['Content-Type'] = 'application\json'
        $Header.Value['Authorization'] = $AuthenticationResult.CreateAuthorizationHeader()
        $Header.Value["x-ms-client-request-id"] = $TaskId + "/" + (New-Guid).ToString() + "-" + (Get-Date).ToString("u")
    }
    catch
    {
        $ErrorMessage = ("Get-BearerToken: failed with [Exception: {0}]." -f $_.Exception)
        Write-Tracing -Level ErrorLevel -Message $ErrorMessage -DisplayMessageToUser
        Throw-TerminatingErrorMessage -Message $ErrorMessage
    }
}

function Get-ProtectionContainerToBeModified([ref] $ContainerMappingList)
{
    try
    {
        Write-InformationTracing ("Get protection container mappings : {0}." -f $VaultResourceId)
        $ContainerMappingListUrl = $ArmEndPoint + $VaultResourceId + "/replicationProtectionContainerMappings" + "?api-version=" + $AsrApiVersion

        Write-InformationTracing ("Getting the bearer token and the header.")
        Get-Header ([ref]$Header) $AadAudience $AadAuthority $RunAsConnectionName

        $Result = @()
        Invoke-InternalRestMethod -Uri $ContainerMappingListUrl -Headers $header -Result ([ref]$Result)
        $ContainerMappings = $Result[0]

        Write-InformationTracing ("Total retrieved container mappings: {0}." -f $ContainerMappings.Value.Count)
        foreach($Mapping in $ContainerMappings.Value)
        {
            if(($Mapping.properties.providerSpecificDetails -eq $null) -or ($Mapping.properties.providerSpecificDetails.instanceType -ine "A2A"))
            {
                Write-InformationTracing ("Mapping properties: {0}." -f ($Mapping.properties))
                Write-InformationTracing ("Ignoring container mapping: {0} as the provider does not match." -f ($Mapping.Id))
                continue;
            }

            if($Mapping.Properties.State -ine "Paired")
            {
                Write-InformationTracing ("Ignoring container mapping: {0} as the state is not paired." -f ($Mapping.Id))
                continue;
            }

            Write-InformationTracing ("Provider specific details {0}." -f ($Mapping.properties.providerSpecificDetails))
            $MappingAutoUpdateStatus = $Mapping.properties.providerSpecificDetails.agentAutoUpdateStatus
            $MappingAutomationAccountArmId = $Mapping.properties.providerSpecificDetails.automationAccountArmId
            $MappingHealthErrorCount = $Mapping.properties.HealthErrorDetails.Count

            if($AutoUpdateAction -ieq "Enabled" -and
                ($MappingAutoUpdateStatus -ieq "Enabled") -and
                ($MappingAutomationAccountArmId -ieq $AutomationAccountArmId) -and
                ($MappingHealthErrorCount -eq 0))
            {
                Write-InformationTracing ("Provider specific details {0}." -f ($Mapping.properties))
                Write-InformationTracing ("Ignoring container mapping: {0} as the auto update is already enabled and is healthy." -f ($Mapping.Id))
                continue;
            }

            ($ContainerMappingList.Value).Add($Mapping.id)
        }
    }
    catch
    {
        $ErrorMessage = ("Get-ProtectionContainerToBeModified: failed with [Exception: {0}]." -f $_.Exception)
        Write-Tracing -Level ErrorLevel -Message $ErrorMessage -DisplayMessageToUser
        Throw-TerminatingErrorMessage -Message $ErrorMessage
    }
}

$OperationStartTime = Get-Date
$ContainerMappingList = New-Object System.Collections.Generic.List[System.String]
$JobsInProgressList = @()
$JobsCompletedSuccessList = @()
$JobsCompletedFailedList = @()
$JobsFailedToStart = 0
$JobsTimedOut = 0
$Header = @{}

$AzureRMProfile = Get-Module -ListAvailable -Name AzureRM.Profile | Select Name, Version, Path
$AzureRmProfileModulePath = Split-Path -Parent $AzureRMProfile.Path
Add-Type -Path (Join-Path $AzureRmProfileModulePath "Microsoft.IdentityModel.Clients.ActiveDirectory.dll")

$Inputs = ("Tracing inputs VaultResourceId: {0}, Timeout: {1}, AutoUpdateAction: {2}, AutomationAccountArmId: {3}." -f $VaultResourceId, $Timeout, $AutoUpdateAction, $AutomationAccountArmId)
Write-Tracing -Message $Inputs -Level Informational -DisplayMessageToUser
$CloudConfig = ("Tracing cloud configuration ArmEndPoint: {0}, AadAuthority: {1}, AadAudience: {2}." -f $ArmEndPoint, $AadAuthority, $AadAudience)
Write-Tracing -Message $CloudConfig -Level Informational -DisplayMessageToUser
$AutomationConfig = ("Tracing automation configuration RunAsConnectionName: {0}." -f $RunAsConnectionName)
Write-Tracing -Message $AutomationConfig -Level Informational -DisplayMessageToUser

ValidateInput
$SubscriptionId = Initialize-SubscriptionId
Get-ProtectionContainerToBeModified ([ref]$ContainerMappingList)

$Input = @{
  "properties"= @{
    "providerSpecificInput"= @{
        "instanceType" = "A2A"
        "agentAutoUpdateStatus" = $AutoUpdateAction
        "automationAccountArmId" = $AutomationAccountArmId
    }
  }
}
$InputJson = $Input |  ConvertTo-Json

if ($ContainerMappingList.Count -eq 0)
{
    Write-Tracing -Level Succeeded -Message ("Exiting as there are no container mappings to be modified.") -DisplayMessageToUser
    exit
}

Write-InformationTracing ("Container mappings to be updated has been retrieved with count: {0}." -f $ContainerMappingList.Count)

try
{
    Write-InformationTracing ("Start the modify container mapping jobs.")
    ForEach($Mapping in $ContainerMappingList)
    {
    try {
            $UpdateUrl = $ArmEndPoint + $Mapping + "?api-version=" + $AsrApiVersion
            Get-Header ([ref]$Header) $AadAudience $AadAuthority $RunAsConnectionName

            $Result = @()
            Invoke-InternalWebRequest -Uri $UpdateUrl -Headers $Header -Method 'PATCH' `
                -Body $InputJson  -ContentType "application/json" -Result ([ref]$Result)
            $Result = $Result[0]

            $JobAsyncUrl = $Result.Headers['Azure-AsyncOperation']
            Write-InformationTracing ("The modify container mapping job invoked with async url: {0}." -f $JobAsyncUrl)
            $JobsInProgressList += $JobAsyncUrl;

            # Rate controlling the set calls to maximum 60 calls per minute.
            # ASR throttling for set calls is 200 in 1 minute.
            Start-Sleep -Milliseconds 1000
        }
        catch{
            Write-InformationTracing ("The modify container mappings job creation failed for: {0}." -f $Ru)
            Write-InformationTracing $_
            $JobsFailedToStart++
        }
    }

    Write-InformationTracing ("Total modify container mappings has been initiated: {0}." -f $JobsInProgressList.Count)
}
catch
{
    $ErrorMessage = ("Modify container mapping jobs failed with [Exception: {0}]." -f $_.Exception)
    Write-Tracing -Level ErrorLevel -Message $ErrorMessage -DisplayMessageToUser
    Throw-TerminatingErrorMessage -Message $ErrorMessage
}

try
{
    while($JobsInProgressList.Count -ne 0)
    {
        Sleep -Seconds 30
        $JobsInProgressListInternal = @()
        ForEach($JobAsyncUrl in $JobsInProgressList)
        {
            try
            {
                Get-Header ([ref]$Header) $AadAudience $AadAuthority $RunAsConnectionName
                $Result = Invoke-RestMethod -Uri $JobAsyncUrl -Headers $header
                $JobState = $Result.Status
                if($JobState -ieq "InProgress")
                {
                    $JobsInProgressListInternal += $JobAsyncUrl
                }
                elseif($JobState -ieq "Succeeded" -or `
                    $JobState -ieq "PartiallySucceeded" -or `
                    $JobState -ieq "CompletedWithInformation")
                {
                    Write-InformationTracing ("Jobs succeeded with state: {0}." -f $JobState)
                    $JobsCompletedSuccessList += $JobAsyncUrl
                }
                else
                {
                    Write-InformationTracing ("Jobs failed with state: {0}." -f $JobState)
                    $JobsCompletedFailedList += $JobAsyncUrl
                }
            }
            catch
            {
                Write-InformationTracing ("The get job failed with: {0}. Ignoring the exception and retrying the next job." -f $_.Exception)

                # The job on which the tracking failed, will be considered in progress and tried again later.
                $JobsInProgressListInternal += $JobAsyncUrl
            }

            # Rate controlling the get calls to maximum 120 calls each minute.
            # ASR throttling for get calls is 10000 in 60 minutes.
            Start-Sleep -Milliseconds 500
        }

        Write-InformationTracing ("Jobs remaining {0}." -f $JobsInProgressListInternal.Count)

        $CurrentTime = Get-Date
        if($CurrentTime -gt $OperationStartTime.AddMinutes($Timeout))
        {
            Write-InformationTracing ("Tracing modify cloud pairing jobs has timed out.")
            $JobsTimedOut = $JobsInProgressListInternal.Count
            $JobsInProgressListInternal = @()
        }

        $JobsInProgressList = $JobsInProgressListInternal
    }
}
catch
{
    $ErrorMessage = ("Tracking modify cloud pairing jobs failed with [Exception: {0}]." -f $_.Exception)
    Write-Tracing -Level ErrorLevel -Message $ErrorMessage  -DisplayMessageToUser
    Throw-TerminatingErrorMessage -Message $ErrorMessage
}

Write-InformationTracing ("Tracking modify cloud pairing jobs completed.")
Write-InformationTracing ("Modify cloud pairing jobs success: {0}." -f $JobsCompletedSuccessList.Count)
Write-InformationTracing ("Modify cloud pairing jobs failed: {0}." -f $JobsCompletedFailedList.Count)
Write-InformationTracing ("Modify cloud pairing jobs failed to start: {0}." -f $JobsFailedToStart)
Write-InformationTracing ("Modify cloud pairing jobs timedout: {0}." -f $JobsTimedOut)

if($JobsTimedOut -gt  0)
{
    $ErrorMessage = "One or more modify cloud pairing jobs has timedout."
    Write-Tracing -Level ErrorLevel -Message ($ErrorMessage)
    Throw-TerminatingErrorMessage -Message $ErrorMessage
}
elseif($JobsCompletedSuccessList.Count -ne $ContainerMappingList.Count)
{
    $ErrorMessage = "One or more modify cloud pairing jobs failed."
    Write-Tracing -Level ErrorLevel -Message ($ErrorMessage)
    Throw-TerminatingErrorMessage -Message $ErrorMessage
}

Write-Tracing -Level Succeeded -Message ("Modify cloud pairing completed.") -DisplayMessageToUser
```

### <a name="manage-updates-manually"></a>Gerenciar atualizações manualmente

1. Se houver novas atualizações para o serviço de mobilidade instalados em suas VMs, você verá a seguinte notificação: A nova atualização do **agente de replicação de recuperação do site está disponível. Clique para instalar.**

   :::image type="content" source="./media/vmware-azure-install-mobility-service/replicated-item-notif.png" alt-text="Janela Itens replicados":::

1. Selecione a notificação para abrir a página de seleção de VM.
1. Escolha as VMs que deseja atualizar e selecione **OK**. O serviço Update Mobility será inicial para cada VM selecionada.

   :::image type="content" source="./media/vmware-azure-install-mobility-service/update-okpng.png" alt-text="Lista de VMs de itens replicados":::

## <a name="common-issues-and-troubleshooting"></a>Problemas comuns e solução de problemas

Se houver um problema com as atualizações automáticas, você verá uma notificação de erro em **Problemas de configuração** no painel do cofre.

Se você não puder habilitar atualizações automáticas, veja os seguintes erros comuns e ações recomendadas:

- **Erro**: você não tem permissões para criar uma conta Executar como do Azure (entidade de serviço) e conceder a função Colaborador à entidade de serviço.

  **Ação recomendada**: Certifique-se de que a conta de entrada assinada seja atribuída como Contribuinte e tente novamente. Para obter mais informações sobre como atribuir permissões, consulte a seção de permissões necessárias de [Como: Usar o portal para criar um aplicativo azure AD e um principal de serviço que possa acessar recursos](/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions).

  Para corrigir a maioria dos problemas depois de habilitar atualizações automáticas, **selecione Reparar**. Se o botão de reparo não estiver disponível, consulte a mensagem de erro exibida no painel de configurações de atualização de extensão.

  :::image type="content" source="./media/azure-to-azure-autoupdate/repair.png" alt-text="Botão de reparo do serviço de recuperação do site em configurações de atualização de extensão":::

- **Erro**: a conta Executar como não tem permissão para acessar o recurso de serviços de recuperação.

  **Ação recomendada :** Excluir e, em seguida, [recriar a conta Executar como .](/azure/automation/automation-create-runas-account) Ou, certifique-se de que o aplicativo Automation Run As Azure Active Directory pode acessar o recurso de serviços de recuperação.

- **Erro**: a conta Executar como não foi encontrada. Um deles foi excluído ou não criado - o aplicativo do Azure Active Directory, a entidade de serviço, a função, o ativo do certificado de automação, o ativo de conexão de automação - ou a impressão digital não é idêntica entre o certificado e a conexão.

  **Ação recomendada :** Excluir e, em seguida, [recriar a conta Executar como .](/azure/automation/automation-create-runas-account)

- **Erro**: O Azure Run as Certificate usado pela conta de automação está prestes a expirar.

  O certificado auto-assinado criado para a conta Run As expira um ano a partir da data de criação. Você pode renová-lo a qualquer momento antes que ele expire. Se você se inscreveu para notificações de e-mail, você também receberá e-mails quando uma ação for necessária do seu lado. Este erro será mostrado dois meses antes da data de validade e mudará para um erro crítico se o certificado tiver expirado. Uma vez que o certificado tenha expirado, a atualização automática não estará funcional até que você renove o mesmo.

  **Ação recomendada**: Para resolver esse problema, **selecione Reparar** e, em seguida, **Renovar certificado**.

  :::image type="content" source="./media/azure-to-azure-autoupdate/automation-account-renew-runas-certificate.PNG" alt-text="renovar-cert":::

  > [!NOTE]
  > Depois de renovar o certificado, atualize a página para exibir o status atual.
