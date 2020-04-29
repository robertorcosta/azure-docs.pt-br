---
title: Enviar um email de um runbook de Automação do Azure
description: Aprenda a usar o SendGrid para enviar um email de dentro de um runbook.
services: automation
ms.subservice: process-automation
ms.date: 07/15/2019
ms.topic: tutorial
ms.openlocfilehash: d4b35458c76da82b33dfcb530cfdc71ee3da3bb6
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604778"
---
# <a name="tutorial-send-an-email-from-an-azure-automation-runbook"></a>Tutorial: Enviar um email de um runbook de Automação do Azure

Você pode enviar um email de um runbook com o [SendGrid](https://sendgrid.com/solutions) usando o PowerShell. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
>
> * Crie um Cofre de chaves do Azure.
> * Armazene sua chave de API do `SendGrid` no cofre de chaves.
> * Crie um runbook reutilizável que recupera sua chave de API e envia um email usando uma chave de API armazenada no [Azure Key Vault](/azure/key-vault/).

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo Az no seu Hybrid Runbook Worker, confira [Instalar o módulo do Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de Automação, você pode atualizar seus módulos para a versão mais recente usando [Como atualizar os módulos do Azure PowerShell na Automação do Azure](automation-update-azure-modules.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, os itens a seguir são necessários:

* Assinatura do Azure: Se você ainda não tiver uma, poderá [ativar os benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se em uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Criar uma Conta do SendGrid](/azure/sendgrid-dotnet-how-to-send-email#create-a-sendgrid-account).
* [Conta de automação](automation-offering-get-started.md) com módulos **Az** e [conexão Executar como](automation-create-runas-account.md) para armazenar e executar o runbook.

## <a name="create-an-azure-key-vault"></a>Criar um Cofre de chaves do Azure

Você pode criar um Azure Key Vault usando o script do PowerShell a seguir. Substitua os valores de variáveis por valores específicos do seu ambiente. Use o Azure Cloud Shell inserido por meio do botão **Experimentar**, localizado no canto superior direito do bloco de código. Você também poderá copiar e executar o código localmente se tiver o [Módulo do Azure PowerShell](/powershell/azure/install-az-ps) instalado no computador local.

> [!NOTE]
> Para recuperar sua chave de API, use as etapas encontradas em [Localizar sua chave de API do SendGrid](/azure/sendgrid-dotnet-how-to-send-email#to-find-your-sendgrid-api-key).

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

Para outras maneiras de criar um Azure Key Vault e armazenar um segredo, confira os [Guias de início rápido do Key Vault](/azure/key-vault/).

## <a name="import-required-modules-to-your-automation-account"></a>Importar os módulos necessários para sua conta de Automação

Para usar o Azure Key Vault em um runbook, sua conta de Automação precisa dos seguintes módulos:

* [Az.Profile](https://www.powershellgallery.com/packages/Az.Profile)
* [Az.KeyVault](https://www.powershellgallery.com/packages/Az.KeyVault)

Clique em **Implantar para Automação do Azure** na guia Automação do Azure em **Opções de Instalação**. Essa ação abre o portal do Azure. Na página Importar, selecione sua conta de Automação e clique em **OK**.

Para conhecer outros métodos para adicionar os módulos necessários, confira [Importar Módulos](/azure/automation/shared-resources/modules#importing-modules).

## <a name="create-the-runbook-to-send-an-email"></a>Criar o runbook para enviar um email

Depois de ter criado um cofre de chaves e armazenado sua chave de API do `SendGrid`, é hora de criar o runbook que vai recuperar a chave de API e enviar um email.

Esse runbook usa `AzureRunAsConnection` como uma [conta Executar como](automation-create-runas-account.md) para autenticação com o Azure a fim de recuperar o segredo do Azure Key Vault.

Use este exemplo para criar um runbook chamado **Send-GridMailMessage**. Você pode modificar o script do PowerShell e reutilizá-lo para diferentes cenários.

1. Vá para sua conta da Automação do Azure.
2. Em **Automação de Processo**, selecione **Runbooks**.
3. Na parte superior da lista de runbooks, selecione **+ Criar um runbook**.
4. Na página **Adicionar Runbook**, insira **Send-GridMailMessage** para o nome do runbook. Para o tipo de runbook, selecione **PowerShell**. Em seguida, selecione **Criar**.
   ![Criar Runbook](./media/automation-send-email/automation-send-email-runbook.png)
5. O runbook é criado e a página **Editar Runbook do PowerShell** se abre.
   ![Editar o Runbook](./media/automation-send-email/automation-send-email-edit.png)
6. Copie o exemplo do PowerShell a seguir para a página **Editar**. Verifique se `$VaultName` é o nome que você especificou ao criar o cofre de chaves.

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

Para verificar se o runbook é executado com êxito, você pode seguir as etapas em [Testar um runbook](manage-runbooks.md#testing-a-runbook) ou [Iniciar um runbook](start-runbooks.md).
Se você não vir inicialmente seu email de teste, verifique suas pastas de **Lixo eletrônico** e **Spam**.

## <a name="clean-up"></a>Limpar

Quando não for mais necessário, exclua o runbook. Para isso, selecione o runbook na lista e clique em **Excluir**.

Exclua o cofre de chaves usando o cmdlet [Remove-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/remove-azkeyvault?view=azps-3.7.0).

```azurepowershell-interactive
$VaultName = "<your KeyVault name>"
$ResourceGroupName = "<your ResourceGroup name>"
Remove-AzKeyVault -VaultName $VaultName -ResourceGroupName $ResourceGroupName
```

## <a name="next-steps"></a>Próximas etapas

* Para problemas ao criar ou iniciar o runbook, confira [Solucionar problemas de erros com runbooks](./troubleshoot/runbooks.md).
* Para atualizar os módulos em sua conta de Automação, confira [Como atualizar os módulos de Azure PowerShell na Automação do Azure](automation-update-azure-modules.md)].
* Para monitorar a execução do runbook, confira [Encaminhar o status do trabalho e fluxos de trabalho de Automação para os logs do Azure Monitor](automation-manage-send-joblogs-log-analytics.md).
* Para disparar um runbook usando um alerta, confira [Usar um alerta para disparar um runbook de Automação do Azure](automation-create-alert-triggered-runbook.md).
