---
title: Enviar um email de um runbook de Automação do Azure
description: Este artigo informa como enviar um email de dentro de um runbook.
services: automation
ms.subservice: process-automation
ms.date: 07/15/2019
ms.topic: conceptual
ms.openlocfilehash: a92f65bd88a5aec79a179a6e2d53de15c274add4
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83834553"
---
# <a name="send-an-email-from-a-runbook"></a>Enviar um email de um runbook

Você pode enviar um email de um runbook com o [SendGrid](https://sendgrid.com/solutions) usando o PowerShell. 

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure. Se você ainda não tiver uma, poderá [ativar os benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se em uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Uma conta do SendGrid](/azure/sendgrid-dotnet-how-to-send-email#create-a-sendgrid-account).
* [Conta da Automação](automation-offering-get-started.md) com módulos **Az**.
* [Conta Executar como](automation-create-runas-account.md) para armazenar e executar o runbook.

## <a name="create-an-azure-key-vault"></a>Criar um Cofre de chaves do Azure

Você pode criar um Azure Key Vault usando o script do PowerShell a seguir. Substitua os valores de variáveis por valores específicos do seu ambiente. Use o Azure Cloud Shell inserido por meio do botão **Experimentar**, localizado no canto superior direito do bloco de código. Você também poderá copiar e executar o código localmente se tiver os [módulos Az](/powershell/azure/install-az-ps) instalados em seu computador local.

> [!NOTE]
> Para recuperar sua chave de API, use as etapas em [Localizar sua chave de API do SendGrid](/azure/sendgrid-dotnet-how-to-send-email#to-find-your-sendgrid-api-key).

```azurepowershell-interactive
$SubscriptionId  =  "<subscription ID>"

# Sign in to your Azure account and select your subscription
# If you omit the SubscriptionId parameter, the default subscription is selected.
Connect-AzAccount -SubscriptionId $SubscriptionId

# Use Get-AzLocation to see your available locations.
$region = "southcentralus"
$KeyVaultResourceGroupName  = "mykeyvaultgroup"
$VaultName = "<Enter a universally unique vault name>"
$SendGridAPIKey = "<SendGrid API key>"
$AutomationAccountName = "testaa"

# Create new Resource Group, or omit this step if you already have a resource group.
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName -Location $region
$resourceId = $newKeyVault.ResourceId

# Convert the SendGrid API key into a SecureString
$Secret = ConvertTo-SecureString -String $SendGridAPIKey -AsPlainText -Force
Set-AzKeyVaultSecret -VaultName $VaultName -Name 'SendGridAPIKey' -SecretValue $Secret

# Grant access to the Key Vault to the Automation Run As account.
$connection = Get-AzAutomationConnection -ResourceGroupName $KeyVaultResourceGroupName -AutomationAccountName $AutomationAccountName -Name AzureRunAsConnection
$appID = $connection.FieldDefinitionValues.ApplicationId
Set-AzKeyVaultAccessPolicy -VaultName $VaultName -ServicePrincipalName $appID -PermissionsToSecrets Set, Get
```

Para ver outras maneiras de criar um Azure Key Vault e armazenar um segredo, confira os [Guias de início rápido do Key Vault](/azure/key-vault/).

## <a name="import-required-modules-into-your-automation-account"></a>Importar os módulos necessários para sua conta da Automação

Para usar o Azure Key Vault em um runbook, é necessário importar os seguintes módulos na sua conta da Automação:

    * [Az.Profile](https://www.powershellgallery.com/packages/Az.Profile)
    * [Az.KeyVault](https://www.powershellgallery.com/packages/Az.KeyVault)

Para obter instruções, consulte [Importar módulos AZ](shared-resources/modules.md#import-az-modules).

## <a name="create-the-runbook-to-send-an-email"></a>Criar o runbook para enviar um email

Depois de criar um cofre de chaves e armazenar sua chave de API do `SendGrid`, é hora de criar o runbook que vai recuperar a chave de API e enviar um email. Vamos usar um runbook que usa `AzureRunAsConnection` como uma [conta Executar como](automation-create-runas-account.md) para autenticação com o Azure a fim de recuperar o segredo no Azure Key Vault. Vamos chamar o runbook de **Send-GridMailMessage**. Você pode modificar o script do PowerShell usado para fins de exemplo e reutilizá-lo para cenários diferentes.

1. Vá para sua conta da Automação do Azure.
2. Em **Automação de Processo**, selecione **Runbooks**.
3. Na parte superior da lista de runbooks, selecione **+ Criar um runbook**.
4. Na página Adicionar Runbook, insira **Send-GridMailMessage** para o nome do runbook. Para o tipo de runbook, selecione **PowerShell**. Em seguida, selecione **Criar**.
   ![Criar Runbook](./media/automation-send-email/automation-send-email-runbook.png)
5. O runbook é criado e a página Editar Runbook do PowerShell é aberta.
   ![Editar o Runbook](./media/automation-send-email/automation-send-email-edit.png)
6. Copie o exemplo do PowerShell a seguir na página Editar. Verifique se o `VaultName` especifica o nome que você escolheu para o Key Vault.

    ```powershell-interactive
    Param(
      [Parameter(Mandatory=$True)]
      [String] $destEmailAddress,
      [Parameter(Mandatory=$True)]
      [String] $fromEmailAddress,
      [Parameter(Mandatory=$True)]
      [String] $subject,
      [Parameter(Mandatory=$True)]
      [String] $content
    )

    $Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Out-Null
    $VaultName = "<Enter your vault name>"
    $SENDGRID_API_KEY = (Get-AzKeyVaultSecret -VaultName $VaultName -Name "SendGridAPIKey").SecretValueText
    $headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
    $headers.Add("Authorization", "Bearer " + $SENDGRID_API_KEY)
    $headers.Add("Content-Type", "application/json")

    $body = @{
    personalizations = @(
        @{
            to = @(
                    @{
                        email = $destEmailAddress
                    }
            )
        }
    )
    from = @{
        email = $fromEmailAddress
    }
    subject = $subject
    content = @(
        @{
            type = "text/plain"
            value = $content
        }
    )
    }

    $bodyJson = $body | ConvertTo-Json -Depth 4

    $response = Invoke-RestMethod -Uri https://api.sendgrid.com/v3/mail/send -Method Post -Headers $headers -Body $bodyJson
    ```

7. Selecione **Publicar** para salvar e publicar o runbook.

Para verificar se o runbook é executado com êxito, siga as etapas em [Testar um runbook](manage-runbooks.md#test-a-runbook) ou [Iniciar um runbook](start-runbooks.md).

Se você não vir inicialmente seu email de teste, verifique suas pastas de **Lixo eletrônico** e **Spam**.

## <a name="clean-up-resources-after-the-email-operation"></a>Limpar recursos após a operação de email

1. Quando o runbook não for mais necessário, selecione-o na lista de runbooks e clique em **Excluir**.

2. Exclua o Key Vault usando o cmdlet [Remove-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/remove-azkeyvault?view=azps-3.7.0).

```azurepowershell-interactive
$VaultName = "<your KeyVault name>"
$ResourceGroupName = "<your ResourceGroup name>"
Remove-AzKeyVault -VaultName $VaultName -ResourceGroupName $ResourceGroupName
```

## <a name="next-steps"></a>Próximas etapas

* Para enviar dados de trabalho do runbook para seu workspace do Log Analytics, consulte [Encaminhar dados de trabalho da Automação do Azure para logs do Azure Monitor](automation-manage-send-joblogs-log-analytics.md).
* Para monitorar logs e métricas de nível de base, consulte [Usar um alerta para disparar um runbook da Automação do Azure](automation-create-alert-triggered-runbook.md).
* Para corrigir problemas decorrentes durante operações de runbook, consulte [Solucionar problemas de runbook](./troubleshoot/runbooks.md).